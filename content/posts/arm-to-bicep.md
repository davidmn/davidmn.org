---
title: "ARM to Bicep"
date: 2023-02-10T00:00:00.00Z
draft: false
---

At work we use Azure for infrastructure and Azure Resource Manager (ARM) templates to define it as code. Microsoft released Bicep as a nicer way to write your templates.

We've recently finished porting a service to use Bicep. It took a lot longer than I'd hoped but we got there. Here are some of the things I learnt along the way.

## Decompiling

You can decompile your existing templates with the Azure cli:

```bash
az bicep decompile -f infra.json
```

The problem is that if you use nested templates you're probably better off writing it from scratch. We found that you'd have to hack the nested template into it's own template and decompile that. Then repeat up the chain. Horrible.

If you've used hyphens in your ARM template then the decompilation will turn them into underscores. Bicep doesn't like hyphens. Not understanding the implications of this would bite us later.

## Fing Around / Finding Out

Then we had to play Chase The Config through our deployment pipeline. 

This was largely self inflicted because we took the opportunity to move to the az command in our pipeline rather than the PowerShell module. When grabbing output from deployments using the az command the format changed and our boilerplate parsing broke. 

It turned out that fetching each output was fewer lines than parsing the old output from an entire template. We could then set the deployment to --no-output which led to much cleaner logs.

Then we encountered a few issues where config was passed around based on convention, meaning that the hyphen-to-underscore conversion caused us to lose some config as the pipeline progressed.

Ironing out these issues led us to find some inconsistencies with our build agents. Low probability errors felt more annoying when you're putting though loads of builds. This drove us to start running our infrastructure deployments in containers. This was a change we should have made earlier but feeling the pain forced us to do actually get around to it.    

Most of the time was spent in this Finding Out phase.

## Was it worth it?

Absolutely. 

The tooling (VSCode + plugins) is so much nicer in Bicep. Having Intellisense/compiler warnings makes extending and refactoring your templates so much easier. Some of our config really upset the ARM plugin for VSCode (SQL filters on queues having single quotes in them which you have to escape would be marked as an error when it's perfectly correct).

These warnings showed us that we'd been supplying pointless/unused parameters to ARM resources for ages. This made our templates more concise instantly. 

Moving away from sub-templates helped us extract common resources into modules more easily. This has allowed us to organise our templates according to features, something like this:

```bash
Infra/
    Feature0/
            feature0.bicep
            feature0-devParameters.json
            feature0-testParameters.json
            feature0-prodParameters.json
    Feature1/
            feature1.bicep
            feature1-devParameters.json
            feature1-testParameters.json
            feature1-prodParameters.json
```

Our collection of small and focused Bicep files are so much more navigable than two thousand lines of JSON.

This was also a great time to make the naming of things more consistent. This service had been worked on by multiple teams over a few years so some inconsistencies had slipped in. 

Bicep having a better concept of resource types also meant we could move away from everything being stringly typed.

Resources having implicit parents rather than needing to explicitly declare what a resource depends on also makes templates more concise.

The last benefit was that I got to take a really close look at and understand what our IaC & deployment pipeline was really doing. I'm still not a DevOps expert but I'm more confident now.

## Unanswered Question

There's one thing I'm still not sure about. We deploy our infrastructure in incremental mode rather than complete. This means if we forget to add a resource or remove it accidentally then it doesn't get blown away on the next deployment. We use this so many layers of templates can be deployed and certain shared resources are safe.

This is great but it does mean you have to carefully inspect the deployment outputs and compare them with known working versions so make sure you've not missed anything. You don't want to end up piggybacking on the success of old ARM based deployments because that will bite you later on.

I've not come across any way of automating this check. Obviously moving to complete mode would solve this but that's way beyond the scope of this project.

## Should you try it?

Yes. Free yourself from writing horrible JSON. Write slightly nicer Bicep.