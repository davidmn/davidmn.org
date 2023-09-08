---
title: "Replacing Postman with Bicep"
date: 2023-09-08T22:00:00.00Z
draft: false
---

As you might have read Postman is [deprecating the Scratchpad feature](https://www.reddit.com/r/webdev/comments/1530mnu/postman_deprecating_scratchpad/). At work we have been using this feature to create and share collections of requests that are useful for manually driving APIs in non prod environments. We commit the collections and environments to repos for posterity. Well now that's going away we've been looking for alternatives. There are other apps like [Insomnia](https://insomnia.rest) which look nice but seem ripe for [enshittification](https://pluralistic.net/2023/01/21/potemkin-ai/). We could write some custom code but that's a lot of effort for a thing we're doing rapidly.

Enter Rider. Over the last few years our team has adopted Rider as our primary IDE. It's got an HTTP Client built in which has entirely solved our problems with a little work.

An HTTP Client takes some descriptions of requests and runs them. Nice and simple. A simple GET request looks a bit like this (these URIs are entirely made up):

```http
# Example Collection

###
# @name Health Check
GET https://example.com/healthcheck
```

It has a collection name, request name, and then the verb and URI. You can run that like a unit test with Rider and see your health check result.

You probably have multiple environments which change your URIs. You can specify these differences in an environment file `http-client.env.json`:

```json
{
    "dev":{
        "baseUri ":"https://dev.example.com",
        "client" :"dev-client"
    },
    "staging":{
        "baseUri" : "https://staging.example.com",
        "client" : "staging-client"
    }
}
```

You can then use the variables defined in the environment file in your requests:

```http
# Example Collection

###
@name Health Check
GET {baseUri}/healthcheck
```

Suppose you want to include some headers on your request, that's easy with some key value pairs below the URI line:

```http
###
@name Health Check
GET {baseUri}/healthcheck
client-tye: {client}
```

Bodies of requests can be included with some JSON after the URI:

```http
###
@name Create User
POST {baseUri}/user/create

{
    "name" : "Gildor Inglorion",
    "hairColour" : "Golden"
}
```

If the bodies of your requests start getting large or you're repeating them all over you can store them in a JSON file and specify them more concisely. Imagine the JSON for Gildor above is in a file at `./bodies/createUser.json` relative to your `.http` file:

```http
###
@name Create User
POST {baseUri}/user/create

> ./bodies/createUser.json

```

I assume your APIs have some access controls. Maybe in non-prod you can call an Identity API with some guff and get a token for that environment (hopefully not for prod). If that's the case you can create a request that gets your token and saves it in your request environment. Extend your environment JSON to this:

```json
{
    "dev":{
        "baseUri ":"https://dev.example.com",
        "client" :"dev-client",
        "bearerToken" : ""
    },
    "staging":{
        "baseUri" : "https://staging.example.com",
        "client" : "staging-client",
        "bearerToken" : ""
    }
}
```

Now you can create a new request that calls your Identity API and call a script that will use the response:

```http
###
@name Get Token
GET https://example.com/token

{
    "env" : "dev"
}

> ./scripts/store-token.js

```

What's `./scripts/store-token.js`? Very simple:

```javascript
client.log("Token :" + response.body.Token)
client.global.set("bearerToken" "Bearer " + response.body.Token)
```

Scripts executed by Rider have access to two variables. `response` gives you access to the response from the request, I'm assuming your Identity API returns the token in the body. `client` provides the interface with Rider, which allows you to log to the output window and save environment variables. So this tiny script sets the value of the `bearerToken` environment variable to the value returned from your Identity API, this data persists between requests you make with the HTTP Client so long as they're in the same environment. If you swap environments with the dropdown then you'll have to get a new token. 

Now you can use the bearer token in future requests:

```http
###
@name Get Gildor
GET https://example.com/users/gildor
Authorization: {{bearerToken}}
```

You can use the Rider's Run/Debug Configurations to create chains of requests. With this you can press one button to get a token and find out about Gildor. Select your GET Gildor request in the Run/Debug Configurations window and add a Before Launch step, select "Run Another Configuration" and select the Get Token request. Check the "Store as project file" box to make this config persist between restarts. Now you can run Get Gildor and it'll run Get Token first (which saves a Bearer token) then Get Gildor.

Finally for some quite frankly niche nonsnese. Maybe your API requires a service token. That token is created using a certificate you have installed on your machine. Create some code (your choice of language) to run on your machine, have that code save the service token to a `.js` file on disk which exports a const. So the output of the code would be writing to `C:\Temp\bearerToken.js` with the content:

```javascript
export const serviceToken = "yourServiceTokenHere"
```

Now in Rider create a script that sets an environment variable to the value in `bearerToken.js` with a script like `loadServiceToken.js`:

```javascript
import {serviceToken} = from "C:\\Temp\\token.js";
client.global.set("serviceToken", "Bearer " + serviceToken)
```

Edit the Run Config of whatever request needs a service token, add a Before Launch step that runs your code that generates a service token. Finally have the HTTP Client run `loadServiceToken.js`:

```http
###
< scripts/loadServiceToken.js
@name Get Gildor
GET https://example.com/users/gildor
Authorization: {{serviceToken}}
```

Now when you run Get Gildor: Rider will execute a Before Launch step that calls your native code, generating a service token that is saved to disk. Then Rider starts the HTTP Client work which executes `loadServiceToken.js` that reads the token from disk and sets it as an environment variable. Finally the HTTP Client makes a GET request using the Service Token in the request header.

This might all seem like a lot. You're right. There's a steep learning curve here but it's very powerful. We've got everything we used in Postman and more. 
 - It's integrated into our development environment (some sort of IDE you say?)
 - The requests are executed directly from the repo, no importing into Postman to run them then exporting any changing
 - Environments are cleaner. Postman can get clogged with loads of very similarly named environment from the various services you've got going on
 
 The only disadvantage is you need a Rider licence. There are alternatives on the way. With the probable downfall of Postman, Visual Studio is adding/improving support for this HTTP Client syntax. At the time of writing it's not very good so we're happy to stick with Rider. There's also a VS Code plugin but that doesn't have the support for the pre-request Javascript which blows the idea out of the water.

I hope this has helped. Let me know if you've got any cool tricks or simplifications.
