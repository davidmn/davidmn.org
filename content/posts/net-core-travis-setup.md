---
title: ".NET Core (2.0) Travis Setup"
date: 2018-03-26T21:58:26.00Z
draft: false
---

It took me about 2 hours but this is what you need to put in your `.travis.yml`

    language: csharp
    solution: dot-net-core-on-linux.sln
    mono: none
    dotnet: 2.1.4
    
    install:
     - dotnet restore
    
    script:
     - dotnet build
     - dotnet test HelloWorld.Tests/HelloWorld.Tests.csproj

Why is this this good?
 
Well you don't need `build.sh` to set the permissions on. That was the first issue. You don't install mono at all which speeds this up no end. Honestly I was testing this with a Hello World application and it was taking 3min to fail which is shite. Without mono it's 90 seconds which is still terrible but I guess you get what you pay for.
 
Obviously you change for your version of dotnet and your solution/test names. Maybe you tweak it for the build process but honestly I'm not your dad.

