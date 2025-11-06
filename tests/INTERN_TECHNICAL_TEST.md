# Selego – Intern Technical Test

**⏱ Time Allocation: 3 hours maximum**

---

## The Problem

You're building a **project budget tracker**. Companies need to track their projects, expenses, and know when they're about to go over budget.

**Your job:** Build a minimal working version that lets users:

1. Create and manage projects with budgets
2. Add expenses to projects
3. See if they're staying within budget
4. Get notified when a project goes over budget (via email)

**What we're testing:**

- Can you design a good data model?
- Can you build clean APIs?
- Can you integrate with external services (email / AI)?
- Can you structure service layers properly?
- Can you ship something that works, fast?
- Do you keep it simple or over-engineer?

---

## Stack

- **Frontend:** React
- **Backend:** Node.js + Express
- **Database:** MongoDB (with Mongoose), [free tier](https://www.mongodb.com/fr-fr/products/platform) available for your test

---

## What to Build

### 1. Backend

Build a simple API with **3 routes**:

#### Main Routes

```javascript
POST / api / projects;
```

Create a project with `{ name, budget }`

```javascript
GET / api / projects;
```

List all projects

```javascript
POST / api / expenses;
```

Add an expense with `{ projectId, title, amount }`

#### Alert Feature

**When the sum of expenses exceeds the budget**:
➡️ **Send an email alert automatically**

You can use:

- [Brevo](https://www.brevo.com/) (free tier available)
- [Resend](https://resend.com/)
- [Mailgun](https://www.mailgun.com/)
- Any other email service you prefer

---

### 2. Frontend

Build a React interface where users can:

- View all projects and their budget status
- Create new projects
- Add expenses to projects
- See when a project is over budget
- Delete things if needed

**We're not providing wireframes or detailed specs.** Use your judgment on:

- What data to display
- How to organize the UI
- What actions users need
- How to show budget alerts

---

### 3. AI Bonus (optional, 30 min max)

If you have time, add a **mini AI feature**:

**Examples:**

- Automatically categorize an expense (marketing, tech, HR, etc.)
- Generate a personalized alert message: _"You're 20% over budget on marketing"_
- Suggestion: _"Your 'Facebook Ads' expense seems to be marketing"_

You can use:

- [OpenRouter](https://openrouter.ai/models?q=free) (lots of different free models)
- [OpenAI API](https://platform.openai.com/)
- [Anthropic Claude](https://www.anthropic.com/)
- Or any other simple service

⚠️ **Optional**: don't spend more than 30 minutes on it. We prefer a clean CRUD over a poorly integrated AI.

---

## Technical Constraints

### Follow These Principles

Based on our [coding standards](https://github.com/selego/whitepaper):

1. **Early Returns** - Use early returns in functions for better readability

   ```javascript
   // ✅ Good
   const { data, ok } = await api.get("/project/123");
   if (!ok) return console.error("Failed to fetch");
   setProject(data);

   // ❌ Bad
   const { data, ok } = await api.get("/project/123");
   if (ok) {
     setProject(data);
   }
   ```

2. **Keep It Simple (KIS)** - Avoid over-abstraction. Be explicit and clear.

   ```javascript
   // ✅ Good - explicit and clear
   <div className="project-list">
     <ProjectCard project={project1} />
     <ProjectCard project={project2} />
     <ProjectCard project={project3} />
   </div>;

   // ❌ Bad - unnecessary abstraction for 3 items
   {
     projects.map((project) => (
       <ProjectCard key={project._id} project={project} />
     ));
   }
   ```

3. **Flat Data Structures** - Avoid nested objects in MongoDB schemas

   ```javascript
   // ✅ Good
   const expenseSchema = {
     projectId: String,
     amount: Number,
     category: String,
   };

   // ❌ Bad
   const projectSchema = {
     expenses: [{ amount: Number, category: String }], // Nested!
   };
   ```

4. **Consistent API Responses** - Always return `{ ok, data }` or `{ ok, error }`

   ```javascript
   // ✅ Good
   return res.status(200).json({ ok: true, data: project });

   // ❌ Bad
   return res.status(200).json(project);
   ```

5. **One Route, One Responsibility** - POST routes create ONE type of object

   ```javascript
   // ✅ Good
   POST /api/project        - Creates a project
   POST /api/expense        - Creates an expense

   // ❌ Bad
   POST /api/project        - Creates project AND its expenses
   ```

---

## Evaluation Criteria

We will evaluate your submission based on:

### 1. Code Quality (40%)

- **Simplicity**: Is the code easy to understand?
- **Readability**: Proper naming, structure, and formatting
- **Consistency**: Following patterns throughout
- **Early returns**: Using them appropriately
- **No over-engineering**: Avoiding unnecessary abstractions

### 2. Architecture & Design (30%)

- **MongoDB schema**: Flat structures, proper relationships
- **API design**: RESTful conventions, consistent naming
- **Error handling**: Graceful failures, proper status codes
- **Data flow**: Clean separation of concerns

### 3. Functionality (20%)

- **Does it work?**: Can we run it and test the features?
- **Completeness**: Are core features implemented?
- **Edge cases**: Basic error handling in place

### 4. Speed & Pragmatism (10%)

- **Time management**: Did you deliver in 2-3 hours?
- **Priorities**: Did you focus on what matters?
- **Working over perfect**: Is it functional vs. over-polished?

---

## Submission Guidelines

### What to Submit

**GitHub Repository** (public or private with access to us)

Your README should include:

- How to set it up and run it
- Any API keys we need (please send the .env with the keys when you send it to us)
- Decisions you made and why
- Time you spent
- What you'd do differently with more time

**Make it runnable:**

- We should be able to `npm install` and run your code
- Document any environment variables needed
- If it requires API keys, provide the .env file

### What We're Looking For

**Red Flags 🚩:**

- Over-complicated solutions with unnecessary abstractions
- Nested data structures in MongoDB (expenses inside projects)
- Poorly structured service files (business logic mixed with API calls)
- Inconsistent patterns and naming across the codebase
- No error handling for external services (email/AI)
- Code that doesn't run or missing core features
- Over-engineered AI features that don't add real value

**Green Flags ✅:**

- Clean, readable code that's easy to understand
- Flat MongoDB schemas with proper references
- Well-structured service layer (separate files for email/AI)
- Consistent API patterns throughout
- Early returns and simple logic
- Working features with good error handling for external APIs
- Good judgment on what to build vs skip
- Simple, practical AI feature that adds value
- Smart trade-offs between speed and quality

---

## Frequently Asked Questions

**Q: How much detail do I need in the data models?**  
A: Use your judgment. Include what makes sense for a budget tracker. We're evaluating your decisions.

**Q: Which email service should I use?**  
A: Pick one you're comfortable with or want to try. Brevo has a generous free tier and simple API. Show us clean service integration.

**Q: When should the email alert be sent?**  
A: You decide. When expenses are added? On a schedule? When budget is exceeded? Justify your choice in the README.

**Q: Which AI feature should I build?**  
A: Pick the one that makes most sense to you or come up with your own. We want to see your judgment. Don't overthink it - simple and working beats complex and broken.

**Q: Do I need to handle AI errors gracefully?**  
A: Yes. APIs fail. Show us how you handle it when the AI service is down or returns an error.

**Q: Can I use a different AI service?**  
A: Absolutely. Use whatever you're comfortable with. We care about the integration pattern, not the specific service.

**Q: Do I need authentication?**  
A: No, skip it. Focus on the core functionality.

**Q: What about styling?**  
A: Make it usable. We're not judging design skills, but it should be functional.

**Q: What if I can't finish in 3 hours?**  
A: Submit what you have. A working partial solution is better than a broken complete one. Be honest about time spent.

**Q: Can I use TypeScript?**  
A: We prefer plain JavaScript, but if you're faster in TypeScript, go ahead.

**Q: Should I use state management libraries?**  
A: Use your judgment. For a small app, local state is probably fine.

**Q: Do I need to actually send emails and call AI APIs?**  
A: Yes, integrate with real services. Use free tiers. We want to see you can integrate external APIs, not mock them.

---

## Getting Started

1. **Set up your project structure**
2. **Design your MongoDB schemas** (think flat!)
3. **Build the backend API** (test with Postman/Insomnia)
4. **Integrate services** (email + AI - keep them simple and modular)
5. **Build the frontend** (connect to your API)
6. **Test everything works** (create project, add expenses, trigger email, use AI)
7. **Clean up and document**
8. **Submit**

---

## Final Notes

We're looking for developers who:

- **Think clearly** about data and architecture
- **Write simple, maintainable code**
- **Ship working features** quickly
- **Make smart trade-offs** under time pressure
- **Follow conventions** and principles

Good luck! We're excited to see what you build. 🚀

---

**Questions?** Email the person who sent you this technical test

**Time starts when you clone this repository.**
