# Selego Technical Assessment - Junior Developer
## Full-Stack Developer Position (Junior Level)

**Time Allocation:** 3-4 hours maximum  
**Stack:** React (frontend) + Node.js/Express (backend) + MongoDB

---

## The Problem

You're building a **simple expense tracker**. Companies need to track their expenses and know how much they're spending.

**Your job:** Build a minimal working version that lets users:
1. Create and manage expense categories
2. Add expenses with amounts and descriptions
3. View total spending by category
4. Send a simple email notification when total expenses exceed a budget limit

**What we're testing:**
- Can you build a basic full-stack application?
- Can you design simple data models?
- Can you build working APIs?
- Can you integrate with one external service (email)?
- Can you keep it simple and functional?

---

## What to Build

### Backend

Build an API that handles:
- **Categories** - like "Food", "Transport", "Office Supplies"
- **Expenses** - with amount, description, and category
- **Budget alerts** - send email when total expenses exceed $1000 (or any limit you choose)

**Service Integration (Required):**

#### Email Service
Send a simple email alert when total expenses exceed your budget limit.

**Recommended Options (pick one):**
- [Brevo/Sendinblue](https://www.brevo.com) - Free tier, simple to use
- [Resend](https://resend.com) - Developer-friendly
- Gmail SMTP (if you're familiar with it)

**We want to see:**
- How you structure your email service code
- How you handle API keys securely (.env file)
- Basic error handling when email fails

### Frontend

Build a React interface where users can:
- View all expenses grouped by category
- Create new expense categories
- Add new expenses to categories
- See total spending
- See a simple alert if over budget
- Delete expenses if needed

**Keep it simple:**
- Basic styling is fine (you can use CSS or any framework you like)
- Focus on functionality over design
- Make sure it's usable and clear

### Data Design

**We'll give you a head start:**

```javascript
// Category Schema
const categorySchema = {
  name: String,        // "Food", "Transport", etc.
  createdAt: Date
};

// Expense Schema  
const expenseSchema = {
  categoryId: ObjectId,  // Reference to category
  amount: Number,        // 25.50
  description: String,   // "Lunch at cafe"
  createdAt: Date
};
```

**Important:** Keep categories and expenses in separate collections (flat structure, not nested).

---

## Technical Guidelines

### Follow These Patterns

Based on our [coding standards](https://github.com/selego/whitepaper):

1. **Use Early Returns** - Exit functions early when conditions aren't met
   ```javascript
   // ✅ Good
   const getExpenses = async (req, res) => {
     const expenses = await Expense.find();
     if (!expenses) return res.status(404).json({ ok: false, error: 'No expenses found' });
     
     return res.status(200).json({ ok: true, data: expenses });
   };
   ```

2. **Keep API Responses Consistent** - Always return `{ ok, data }` or `{ ok, error }`
   ```javascript
   // ✅ Good
   return res.status(200).json({ ok: true, data: expenses });
   
   // ✅ Also good
   return res.status(400).json({ ok: false, error: 'Invalid data' });
   ```

3. **One Route, One Job** - Each API endpoint should do one thing
   ```javascript
   // ✅ Good
   POST /api/categories     - Creates a category
   POST /api/expenses       - Creates an expense
   GET  /api/expenses       - Gets all expenses
   ```

4. **Keep Data Flat** - Don't nest objects in your MongoDB schemas
   ```javascript
   // ✅ Good - separate collections
   Category: { name: "Food" }
   Expense: { categoryId: "123", amount: 25.50 }
   
   // ❌ Bad - nested data
   Category: { 
     name: "Food",
     expenses: [{ amount: 25.50 }]  // Don't do this!
   }
   ```

---

## Helpful Getting Started Guide

### Step 1: Set Up Your Project (30 minutes)
```bash
# Backend setup
mkdir expense-tracker
cd expense-tracker
mkdir backend frontend
cd backend
npm init -y
npm install express mongoose cors dotenv
npm install -D nodemon

# Frontend setup  
cd ../frontend
npx create-react-app . --template javascript
npm install axios
```

### Step 2: Build Backend First (90 minutes)
1. **Set up Express server** with basic routes
2. **Connect to MongoDB** (use MongoDB Atlas free tier)
3. **Create your models** (Category and Expense)
4. **Build API endpoints:**
   - `GET /api/categories` - get all categories
   - `POST /api/categories` - create category
   - `GET /api/expenses` - get all expenses
   - `POST /api/expenses` - create expense
   - `DELETE /api/expenses/:id` - delete expense

### Step 3: Add Email Service (45 minutes)
1. **Sign up for email service** (Brevo is easiest)
2. **Create email service file** (`services/emailService.js`)
3. **Add budget check logic** - when creating expense, check if total > limit
4. **Send email when over budget**

### Step 4: Build Frontend (60 minutes)
1. **Create components:**
   - ExpenseList
   - AddExpense  
   - CategoryList
   - AddCategory
2. **Connect to your API** using axios
3. **Add basic styling** to make it usable

### Step 5: Test Everything (15 minutes)
- Create categories
- Add expenses
- Trigger email by going over budget
- Make sure everything works

---

## Evaluation Criteria

### 1. Does It Work? (50%)
- Can we run your code?
- Do the core features work?
- Can we create expenses and categories?
- Does the email get sent?

### 2. Code Quality (30%)
- Is your code easy to read?
- Do you follow the patterns we showed?
- Is it organized into logical files?
- Do you handle basic errors?

### 3. Problem Solving (20%)
- Did you make good decisions about what to build?
- Did you keep it simple?
- Did you manage your time well?

---

## What We're NOT Looking For

**Don't worry about:**
- Perfect design or styling
- User authentication or login
- Complex validation
- Advanced features
- Perfect error handling for every edge case
- Deployment

**Focus on:**
- Getting the basic functionality working
- Clean, readable code
- Following our patterns
- One working email integration

---

## Submission Guidelines

### What to Submit

**GitHub Repository** with:
- Both frontend and backend code
- README with setup instructions
- .env.example file showing what environment variables we need

**Your README should include:**
- How to install and run your project
- What email service you used and how to set it up
- Any decisions you made
- How much time you spent
- What you found challenging

### Make It Easy to Run
- We should be able to `npm install` and start your project
- Include clear instructions for any setup steps
- If you use API keys, show us how to get them

---

## Example Project Structure

```
expense-tracker/
├── backend/
│   ├── models/
│   │   ├── Category.js
│   │   └── Expense.js
│   ├── routes/
│   │   ├── categories.js
│   │   └── expenses.js
│   ├── services/
│   │   └── emailService.js
│   ├── server.js
│   └── .env.example
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── services/
│   │   └── App.js
└── README.md
```

---

## Frequently Asked Questions

**Q: What if I can't finish everything in 4 hours?**  
A: Submit what you have! A working partial solution is better than nothing. Be honest about time spent.

**Q: Which email service should I use?**  
A: Brevo (Sendinblue) has a good free tier and simple API. But use whatever you're comfortable with.

**Q: When should I send the email alert?**  
A: You decide! Maybe when adding an expense pushes total over $1000? Explain your choice in the README.

**Q: Do I need to make it look pretty?**  
A: No! Focus on functionality. Basic styling is fine.

**Q: Can I use different technologies?**  
A: Stick to the required stack (React + Node.js + MongoDB) so we can evaluate fairly.

**Q: What if the email service is down?**  
A: Handle the error gracefully - don't let it crash your app. Log the error and continue.

**Q: Should I add lots of validation?**  
A: Basic validation is good (required fields, positive numbers), but don't spend too much time on it.

---

## Getting Started Checklist

- [ ] Set up project structure
- [ ] Create MongoDB database (Atlas free tier)
- [ ] Build basic Express server
- [ ] Create Category and Expense models
- [ ] Build API endpoints
- [ ] Set up email service account
- [ ] Add email integration
- [ ] Create React frontend
- [ ] Connect frontend to backend
- [ ] Test everything works
- [ ] Write README
- [ ] Submit!

---

## Final Tips

- **Start simple** - get basic CRUD working first
- **Test as you go** - don't wait until the end
- **Ask for help** if you're stuck (email us at hiring@selego.co!)
- **Focus on working code** over perfect code
- **Document your decisions** in the README

Good luck! We're excited to see what you build. 🚀

---

**Questions?** Email us at hiring@selego.co

**Time starts when you clone this repository.**
