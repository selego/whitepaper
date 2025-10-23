# Selego Technical Assessment - Senior Developer
## Full-Stack Developer Position (Senior Level)

**Time Allocation:** 2-3 hours maximum  
**Stack:** React (frontend) + Node.js/Express (backend) + MongoDB

---

## The Challenge

You're building a **smart contract management platform**. Law firms need to analyze contracts, extract key information, and get AI-powered insights to make better decisions.

**Your mission:** Build a system that demonstrates senior-level thinking in:
1. **AI Integration Architecture** - Beyond basic chatbots, show real AI problem-solving
2. **Client-Ready Code** - Code quality that you'd confidently present to clients
3. **Independent Decision Making** - Make architectural choices and justify them
4. **Technical Leadership** - Show patterns other developers could follow

**What we're really testing:**
- Can you architect AI solutions that solve real business problems?
- Can you write code that inspires confidence in clients and team members?
- Can you work independently and make smart trade-offs under pressure?
- Do you think like a senior developer who can guide technical decisions?

---

## What to Build

### Core System

**Contracts Management:**
- Upload and store contract documents (PDF/text)
- Extract and display contract metadata
- Track contract status and important dates

**AI-Powered Analysis (This is the key test):**
Choose **TWO** of these AI features to implement:

#### Option A: Contract Intelligence
- **Extract key terms** (parties, dates, amounts, clauses)
- **Risk assessment** - AI identifies potential legal risks
- **Compliance checking** - Flag contracts that don't meet company standards

#### Option B: Smart Recommendations  
- **Similar contract matching** - Find contracts with similar terms
- **Pricing analysis** - AI suggests if contract terms are favorable
- **Amendment suggestions** - AI recommends contract improvements

#### Option C: Advanced Document Processing
- **Multi-document analysis** - Compare multiple contracts simultaneously  
- **Trend analysis** - AI identifies patterns across contract portfolio
- **Auto-categorization** - Smart tagging and organization of contracts

**Why this tests real AI skills:**
- Requires understanding of document processing, not just chat
- Tests ability to structure complex AI workflows
- Shows judgment in choosing appropriate AI models/approaches
- Demonstrates handling of real-world data complexity

### Technical Requirements

**Backend Architecture:**
- RESTful API with proper error handling
- File upload and processing pipeline
- AI service integration with fallback strategies
- Database design for complex document relationships

**Frontend Experience:**
- Document upload with progress indicators
- AI results display with confidence levels
- Interactive contract analysis dashboard
- Professional UI that you'd demo to a client

**Integration & Services:**
- **AI Service** - OpenAI, Anthropic, or your choice
- **Email Notifications** - Contract deadline alerts
- **File Storage** - Handle document uploads securely

---

## Senior-Level Expectations

### 1. AI Architecture (40%)
**We want to see:**
- **Strategic AI usage** - When to use AI vs traditional logic
- **Error resilience** - Graceful handling of AI failures
- **Performance optimization** - Efficient API usage and caching
- **Data preprocessing** - Smart document parsing and preparation
- **Result validation** - How do you verify AI output quality?

**Red flags:**
- Using AI for everything without thinking
- No error handling for AI service failures  
- Inefficient API usage (sending full documents repeatedly)
- No consideration for AI response reliability

### 2. Code Architecture (30%)
**We want to see:**
- **Service layer separation** - Clean abstraction of business logic
- **Scalable patterns** - Code that could handle growth
- **Error boundaries** - Comprehensive error handling strategy
- **Configuration management** - Proper environment handling
- **Code documentation** - Comments where they add value

### 3. Client-Ready Quality (20%)
**We want to see:**
- **Professional UI/UX** - Something you'd confidently demo
- **Comprehensive README** - Clear setup and usage instructions
- **API documentation** - Endpoints documented for team use
- **Deployment readiness** - Environment configuration, build process
- **Security considerations** - Basic security best practices

### 4. Technical Leadership (10%)
**We want to see:**
- **Decision justification** - Explain your architectural choices
- **Trade-off awareness** - What you'd do differently with more time
- **Team considerations** - Code patterns others could follow
- **Future planning** - How would this scale or evolve?

---

## Evaluation Scenarios

### Scenario 1: Client Demo
*"You need to demo this to a law firm partner in 30 minutes. They're technical enough to ask good questions but want to see real value."*

**What this tests:**
- Is your UI professional and intuitive?
- Can you explain your AI choices confidently?
- Does the system actually work reliably?
- Would you feel confident presenting this?

### Scenario 2: Team Handoff
*"A junior developer needs to add a new contract type. Can they understand and extend your code?"*

**What this tests:**
- Code organization and documentation
- Consistent patterns and naming
- Service layer abstraction
- Configuration management

### Scenario 3: Production Readiness
*"The client wants to deploy this next week. What needs to happen?"*

**What this tests:**
- Environment configuration
- Error handling and monitoring
- Security considerations
- Scalability planning

---

## Submission Requirements

### Code Quality Checklist
- [ ] **Clean architecture** - Services, routes, and utilities properly separated
- [ ] **Error handling** - Graceful failures with proper logging
- [ ] **Configuration** - Environment variables properly managed
- [ ] **Documentation** - README, API docs, and inline comments where helpful
- [ ] **Security** - API keys secure, input validation, basic security headers

### AI Implementation Checklist
- [ ] **Two AI features** implemented and working
- [ ] **Error resilience** - System works even when AI fails
- [ ] **Performance** - Efficient API usage, appropriate caching
- [ ] **Validation** - Some method to verify AI output quality
- [ ] **User feedback** - Clear indication of AI confidence/reliability

### Professional Presentation Checklist
- [ ] **Demo-ready UI** - Professional enough for client presentation
- [ ] **Clear setup** - Someone else can run your code easily
- [ ] **Decision documentation** - Explain your architectural choices
- [ ] **Future planning** - What would you do with more time/resources?

---

## Technical Constraints & Principles

Follow Selego's coding standards:

1. **Early Returns** - Clean, readable control flow
2. **Flat Data Structures** - Avoid nested MongoDB documents
3. **Consistent API Responses** - Always `{ ok, data }` or `{ ok, error }`
4. **Service Layer Separation** - Business logic separate from routes
5. **Keep It Simple** - Avoid over-engineering, be explicit and clear

---

## Time Management Strategy

**Hour 1: Foundation & Planning**
- Choose your two AI features (15 min)
- Set up project structure (15 min)
- Design data models and API structure (15 min)
- Build basic CRUD operations (15 min)

**Hour 2: AI Implementation**
- Implement first AI feature (30 min)
- Implement second AI feature (30 min)

**Hour 3: Integration & Polish**
- Frontend development (30 min)
- Error handling and edge cases (15 min)
- Documentation and final testing (15 min)

---

## Bonus Challenges (If You Finish Early)

- **Real-time updates** - WebSocket integration for processing status
- **Batch processing** - Handle multiple document uploads
- **Advanced caching** - Redis or in-memory caching for AI results
- **API rate limiting** - Protect against abuse
- **Simple analytics** - Track usage patterns
- **Export functionality** - Generate reports from AI analysis

---

## What We're Looking For

### Green Flags ✅
- **Thoughtful AI integration** that solves real problems
- **Professional code quality** you'd confidently show clients
- **Smart architectural decisions** with clear reasoning
- **Comprehensive error handling** for production scenarios
- **Clear documentation** that enables team collaboration
- **Performance awareness** in AI API usage
- **Security mindset** in handling documents and data

### Red Flags 🚩
- **AI for AI's sake** without clear business value
- **Fragile integrations** that break when services fail
- **Over-engineered solutions** that sacrifice clarity
- **Poor error handling** especially for external services
- **Unprofessional UI** that you wouldn't demo to clients
- **Hard-coded values** or poor configuration management
- **No consideration** for team collaboration or handoff

---

## Success Criteria

**Minimum Viable (Pass):**
- Two AI features working with basic error handling
- Clean, readable code following our principles
- Professional UI that demonstrates the features
- Clear setup instructions

**Strong Performance (Hire):**
- Thoughtful AI architecture with performance considerations
- Code quality that inspires confidence in technical leadership
- Professional presentation ready for client demos
- Clear documentation of decisions and trade-offs

**Exceptional (Strong Hire):**
- Innovative AI solutions that show deep understanding
- Architecture that other developers would want to emulate
- Production-ready considerations (security, scalability, monitoring)
- Clear technical leadership and mentoring potential

---

## Questions & Support

**Q: Which AI features should I choose?**
A: Pick the ones that interest you most or that you think would be most valuable. We want to see your judgment and technical approach.

**Q: How sophisticated should the AI be?**
A: Focus on solid implementation over complexity. A well-architected simple feature beats a broken complex one.

**Q: What if AI services are unreliable during development?**
A: Handle it gracefully and document the issue. This is exactly what we want to see - real-world resilience.

**Q: How much should I focus on UI vs backend?**
A: Balance both, but prioritize functionality and code quality. The UI should be professional but doesn't need to be pixel-perfect.

---

**Questions?** Email us at hiring@selego.co

**Time starts when you clone this repository.**

---

## Final Note

This assessment simulates the kind of work you'd do at Selego - building AI-powered solutions for clients who expect professional quality and reliable systems. Show us you can deliver both technical excellence and client-ready results under time pressure.

We're excited to see how you approach this challenge! 🚀
