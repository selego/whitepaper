# Mono Repo Simplicity

### Disclaimer: Clarifying the Monorepo Approach at Le Stud

In discussions about the monorepo structure at Le Stud, it's important to set the right context for what we mean by this term. 

Generally, 'monorepo' refers to a single repository that not only consolidates code in a common folder but also often includes a shared `package.json` for library management across different components. However, our vision at Le Stud is slightly different.

For us, a monorepo signifies a singular, cohesive repository that houses all the necessary elements for a complete project—be it the API, application, or other integral parts. What sets our approach apart is our decision **not to use a shared `package.json` across various projects or components within the monorepo**. Each project within our monorepo is treated as its independent entity, with its own set of libraries and dependencies.

This distinction is crucial for our team members to understand. By adopting the monorepo format, we aim for organizational simplicity and streamlined project management. However, we also value the autonomy and specific needs of each project, allowing them to operate within their own technical frameworks and requirements. This approach reflects our dedication to flexibility and tailored project handling, ensuring that each initiative under Le Stud is optimally equipped for success.

### Consider the repositories for SELEGO

We have around 200 repositories. That is already too many to manage. Now, imagine every part of every project had its own repo. That would mean at least a good 600 repositories.

Think of somebody onboarding: instead of only having to clone `jobmaker`, they’d have to clone `jobmaker-api` and `jobmaker-app`. Now imagine having separate apps for HR, a landing page, and a Teams app. It’s already too much to keep track of.

But here’s a screenshot of our beautiful monorepo—**No unknown unknowns** for a newcomer. 



<div style="display: flex; align-items: center; margin-bottom: 30px;">
  <img src="https://bank.cellar-c2.services.clever-cloud.com/file/project/96916bab74c0cf10773bb20bc911d109/Screenshot%202024-10-05%20at%2010.26.46.png" alt="Microservices War" style="width: 900px; margin-right: 30px;">
  <div>
</div>


### Easy Bug Fixing

Imagine that you have a separate repo for your backend and your frontend. We’re full stack engineers. Usually fixing a bug (without adding technical debt) consists of doing fixes both in the frontend and in the backend. If you have two repos, that’s two git pushes, two pull requests for two lines of code that are highly related. 

But in a monorepo, you can fix the bug in a single PR (and this is coming from someone who hates big PRs—so please keep it small in all cases).

### We Work in Small Teams

Monorepos are best for small teams. Again, we do a bit of everything, so the easier the switch, the better. 

### Why Google Stores Billions of Lines of Code in a Single Repository
