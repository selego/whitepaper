# Selego Technical Assessment - Intermediate Developer
## Full-Stack Developer Position (2-4 Years Experience)

**Time Allocation:** 2.5-3 hours maximum  
**Stack:** React (frontend) + Node.js/Express (backend) + MongoDB

---

## The Problem

You're building a **team expense management system**. Companies need to track expenses across different teams, manage budgets per team, and get insights into spending patterns.

**Your job:** Build a working system that lets users:
1. Create and manage teams with individual budgets
2. Add expenses to specific teams
3. Track spending against team budgets
4. Send email alerts when teams exceed their budgets
5. Add one AI-powered feature for expense insights

**What we're testing:**
- Can you design normalized data relationships?
- Can you build clean, scalable APIs?
- Can you integrate multiple external services efficiently?
- Can you implement business logic with proper validation?
- Can you balance feature completeness with time constraints?

---

## What to Build

### Backend

Build an API that handles:
- **Teams** - with names, budgets, and members
- **Expenses** - linked to teams, with categories and approval status
- **Budget monitoring** - track spending vs. budget per team
- **Email notifications** - when teams exceed budget thresholds

**Service Integrations (Required):**

#### 1. Email Service
Send budget alerts when a team's expenses exceed 80% and 100% of their budget.

**Recommended Options:**
- [Brevo/Sendinblue](https://www.brevo.com) - Free tier, good API
- [Resend](https://resend.com) - Developer-friendly
- [Mailgun](https://www.mailgun.com) - Reliable choice

#### 2. AI Integration
Add **one AI-powered feature** to enhance the expense management:

**Choose one:**
- **Smart categorization** - AI suggests expense categories based on description
- **Budget forecasting** - AI predicts if team will exceed budget based on current trends
- **Expense validation** - AI flags potentially duplicate or suspicious expenses
- **Spending insights** - AI provides brief analysis of team spending patterns

**AI Service Options:**
- [OpenAI API](https://platform.openai.com) - GPT models
- [Anthropic Claude](https://www.anthropic.com) - Alternative to GPT
- [OpenRouter](https://openrouter.ai) - Multiple models

**We want to see:**
- Clean service layer architecture
- Proper error handling for external APIs
- Secure API key management
- Smart integration points (when to call AI vs when not to)

### Frontend

Build a React interface with:
- **Team dashboard** - showing budget status for each team
- **Expense management** - add/edit/delete expenses per team
- **Budget alerts** - visual indicators when teams are over/near budget
- **AI insights** - display results from your chosen AI feature
- **Basic filtering** - by team, date range, or category

**Technical expectations:**
- Component organization and reusability
- State management (local state is fine, but show good patterns)
- Error handling for API calls
- Loading states for async operations

### Data Design

Design your own schemas, but consider:
- How to efficiently query expenses by team
- How to calculate budget utilization
- How to handle expense categories
- Whether to store calculated totals or compute them

**Hint:** Think about query patterns and data consistency.

---

## Technical Requirements

### Follow These Principles

1. **API Design** - RESTful conventions with consistent responses
   ```javascript
   // ✅ Good API structure
   GET    /api/teams              - List all teams
   GET    /api/teams/:id          - Get team details
   POST   /api/teams              - Create team
   GET    /api/teams/:id/expenses - Get team expenses
   POST   /api/expenses           - Create expense
   ```

2. **Error Handling** - Graceful failures with proper status codes
   ```javascript
   // ✅ Good error handling
   try {
     const result = await emailService.sendAlert(team, budget);
     if (!result.success) {
       console.error('Email failed:', result.error);
       // Continue processing, don't crash
     }
   } catch (error) {
     console.error('Email service error:', error);
   }
   ```

3. **Service Layer** - Separate business logic from routes
   ```javascript
   // ✅ Good separation
   // routes/expenses.js
   router.post('/', async (req, res) => {
     const result = await expenseService.createExpense(req.body);
     return res.status(result.status).json(result);
   });
   
   // services/expenseService.js
   const createExpense = async (expenseData) => {
     // validation, business logic, database operations
   };
   ```

4. **Data Validation** - Validate inputs and handle edge cases
   ```javascript
   // ✅ Good validation
   const expenseSchema = {
     teamId: { type: ObjectId, required: true },
     amount: { type: Number, required: true, min: 0.01 },
     description: { type: String, required: true, trim: true },
     category: { type: String, enum: ['travel', 'food', 'supplies', 'other'] }
   };
   ```

---

## Evaluation Criteria

### 1. Architecture & Design (35%)
- **Data modeling**: Efficient schemas and relationships
- **API design**: RESTful, consistent, well-structured
- **Service layer**: Clean separation of concerns
- **Error handling**: Graceful failures, proper logging

### 2. Code Quality (30%)
- **Readability**: Clear naming, good structure
- **Patterns**: Consistent approaches throughout
- **Validation**: Input validation and edge case handling
- **Testing mindset**: Code that would be easy to test

### 3. Integration Skills (25%)
- **External APIs**: Clean integration with email and AI services
- **Error resilience**: Handling service failures gracefully
- **Security**: Proper API key management
- **Performance**: Efficient API usage

### 4. Feature Completeness (10%)
- **Core functionality**: Does everything work as expected?
- **User experience**: Is the interface usable and intuitive?
- **Edge cases**: Basic handling of error scenarios

---

## Bonus Points (Optional)

If you finish early and want to showcase additional skills:
- **Input validation** with proper error messages
- **Pagination** for expense lists
- **Export functionality** (CSV/PDF reports)
- **Simple charts** showing budget utilization
- **Caching** for expensive operations
- **Basic tests** for critical functions

**Note:** Only attempt these if core functionality is solid!

---

## Submission Guidelines

### What to Submit

**GitHub Repository** with:
- Clean, organized code structure
- Comprehensive README with setup instructions
- .env.example with all required variables
- Any additional documentation you think is helpful

**Your README should include:**
- Setup and installation steps
- API documentation (endpoints and expected responses)
- Which AI feature you chose and why
- Architecture decisions you made
- Time spent and what you'd improve with more time
- Any assumptions or trade-offs you made

### Technical Requirements
- Code should run with `npm install` and `npm start`
- Include database setup instructions or seed data
- Document any external service setup needed
- Provide test API keys or clear instructions for getting them

---

## What We're Looking For

**Strong Signals ✅:**
- Well-structured, maintainable code
- Thoughtful API design with consistent patterns
- Clean integration with external services
- Good error handling and edge case consideration
- Smart trade-offs between features and time
- Clear documentation and setup instructions
- Practical AI feature that adds real value

**Warning Signs 🚩:**
- Over-engineered solutions with unnecessary complexity
- Poor error handling (especially for external services)
- Inconsistent patterns across the codebase
- Hard-coded credentials or poor security practices
- AI features that don't work or add no value
- Missing core functionality in favor of polish
- Unclear or missing documentation

---

## Time Management Suggestions

**Hour 1: Foundation**
- Set up project structure
- Design data schemas
- Build basic CRUD APIs
- Test with Postman/Insomnia

**Hour 2: Integrations**
- Implement email service
- Add budget monitoring logic
- Choose and implement AI feature
- Handle errors gracefully

**Hour 3: Frontend & Polish**
- Build React components
- Connect to backend APIs
- Add loading states and error handling
- Test end-to-end functionality

**Final 30 minutes:**
- Clean up code
- Write documentation
- Test setup instructions

---

## Frequently Asked Questions

**Q: How complex should the AI feature be?**  
A: Keep it simple but functional. A working basic feature is better than a complex broken one. Focus on clean integration patterns.

**Q: Should I use TypeScript?**  
A: Use what you're most productive with. We care more about code quality than the specific language variant.

**Q: How much validation do I need?**  
A: Basic validation for required fields and data types. Don't spend too much time on complex validation rules.

**Q: What if external services fail during development?**  
A: Handle it gracefully in your code and document the issue. We understand external dependencies can be unreliable.

**Q: Should I deploy the application?**  
A: Not required, but if you can do it quickly and it doesn't take away from core functionality, it's a nice touch.

**Q: How detailed should my API documentation be?**  
A: Include endpoints, expected request/response formats, and any important notes. A simple table or list is fine.

---

## Getting Started

1. **Plan your approach** (15 minutes)
   - Sketch out your data models
   - Choose your external services
   - Plan your AI feature

2. **Set up the foundation** (45 minutes)
   - Project structure
   - Database connection
   - Basic API routes

3. **Build core features** (60 minutes)
   - CRUD operations
   - Business logic
   - Budget calculations

4. **Add integrations** (45 minutes)
   - Email service
   - AI feature
   - Error handling

5. **Frontend development** (45 minutes)
   - React components
   - API integration
   - Basic styling

6. **Testing and documentation** (30 minutes)
   - End-to-end testing
   - README documentation
   - Final cleanup

---

## Final Notes

We're looking for developers who can:
- **Balance speed with quality** under time pressure
- **Make smart architectural decisions** for maintainable code
- **Integrate external services** cleanly and reliably
- **Think about user experience** while building features
- **Document their work** clearly for team collaboration

This assessment simulates real project work where you need to deliver working features quickly while maintaining code quality.

Good luck! 🚀

---

**Questions?** Email us at hiring@selego.co

**Time starts when you clone this repository.**
