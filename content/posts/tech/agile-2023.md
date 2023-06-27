---
title: "Agile 2023"
date: 2023-06-27T17:33:14+01:00
draft: false
---

Nearly [three years ago](/posts/tech/agile) I wrote about my ideas on Agile. Those ideas have evolved a little since then. It's honestly just getting closer to Extreme Programming (XP).

I'm back to using Kanban at work and I can't imagine going back to Scrum. At least for the ongoing maintenance combined with feature work I am doing currently.

What do I think now?

Just bin sprint reviews. Opt for just talking to people about what you're working on. Maybe have a periodic session for knowledge sharing but only present stuff you think will be interesting or useful to other teams. Not just a list of tickets you've finished.

Use Just in Time Refinement (JiTR). Create a backlog of stories that are unrefined (with titles and a little sketch of what's involved or links to documentation). Have a couple of sessions for refinement booked in the diary each week. On the day of refinement decide if you actually need more stories. If you don't think your stories will last until the next refinement, do some refining. We tried truly JiTR with no scheduled meetings but that made syncing up with Architects and Business Analysts (BAs) really hard.

If you're finding a refining a story hard because it's unknown. Do a spike of about two days with the outcome of it a semi refined story ready to take back to refinement. Iterative.

Bin story points. It's freeing. Instead, favour splitting your stories more than you would normally. We've created a few useful heuristics to help you find seams to split along:

 - Can it be split by deployable component? e.g. per pipleline 
 - Can validation be split off into a separate story? Probably do that first if you can.
 - Are there individual code changes that can go to production separately? Exercising your release pipeline is good.
 - Can this feature become a walking skeleton first? Essentially no-op the business logic but have the API in place.

Ask yourself "Can I finish this story in about 2 days?". Sometimes you can't. Sometimes you'll need to take on a monumental port or rewrite just to work out how you would have split it in the first place. That's fine. Remember those seams and do it better the next time. Remember the iterative process of XP?

A real example was a project to move an application running on a a virtual machine as a Windows service (deployed via Octopus Deploy) to ServiceFabric (deployed via Azure Devops). It also included an move from .NET Framework to .NET 6. It took over 200 commits and many many weeks. After finishing it we reflected on what caused us pain. We created a hierarchy of refactorings to apply which could each be deployed to production rather than a big bang release. In effect these are seams which you can split your stories. They are as follows:

1. Move as many secrets to a secret manager as possible. Pushing them to KeyVault while you're deploying your Infrastructure as Code.
2. Move the rest of your config to environment variables. Say goodbye to App.config and their friends.
3. Now take your code and make it a guest exe in ServiceFabric[0]. This step is fiddly because SF is fiddly by nature.
4. Now deploy that project via Devops (we have a nice pipleine template).
5. Do your .NET upgrades.

Our first attempt at doing such a move did all five steps in one ticket. Our hierarchy of refactoring is much less risky and easier to keep in your head. You can still get blocked of course, but it's a lot more breezy than a big bang release.

So now you've binned sprint reviews, a huge backlog, and story points. You've got a tight Kanban board with enough work to get you through the week. You can react quickly to changing requirements because you don't have towers of stories that need re-refining. 

Obviously now you should bin sprint planning. Because you're not doing sprints. Keeping on top of what's coming up in the longer term is a little harder now. I'm not sure we've really nailed that yet. The team lead seems to end up fielding a lot of the messaging from the business which could maybe be done by a Business Analyst or Product Owner.

Now here's a really spicy take. If the business changes its priorities and you're going to shelve some upcoming work, delete the story. Resist the urge to keep it. I know it's hard. I'm still finding it hard, but there's a strong possibility you'll never come back to it. In that case you've already cleaned your backlog. If you do come back to it weeks or months later, how much do you trust your old refinement? You'll have lost all the context you had when refining the first time. It's probably better to just re-refine it if you need it.

Sometimes you'll have to estimate how long a feature will take. Some call this T-Shirt Sizing. I think it's more like this meme:

![Futile](/content/images/2023/06/language.jpeg)

Have a chat with the team. Sketch out the stories as much as you can. Split them using the above. Multiply the number of split stories by Pi to get the number of days. Round up to the nearest week.

Now for some more traditional XP practices I find useful. Pair as much as you can. Randomise the pairs every day if possible. If you're remote, spin up calls in your team's General channel so you can flip between them to ask questions. Just like you would in the office. Use a pomodoro timer to swap driver every 30min or so. If you've got a particularly gnarly problem or you've only got 3 people in, don't be afraid to mob. This time swap every 20min or so.

Comparing these ideas with my last post is very interesting. Shifting towards a more self organising team and being more iterative. Stripping away the corporate ceremony. Slowly re-deriving XP-like ideas. In some ways looking a little more like a start up while also being the least Greenfield job I've ever had[1]. It's almost like Beck and Cunningham knew what they were talking about eh?

[0] - Now there's actually a secret additional splitting pattern you can do here. Create a blank ServiceFabric project that goes to production first. Then add your secrets and environment variables, take that to production. Now move over your business logic and work out how to do the switch over.

[1] - It's also the longest I've been at a company. Sticking around to see the consequences of your decisions is a great learning experience.