# Selego Style Guide

## Introduction
This guide provides standards and best practices for developing projects at Selego, ensuring consistency, readability, and maintainability of our code.

### Purpose
To establish a consistent coding style and workflow for all projects, making collaboration easier and reducing technical debt.

### Onboarding Materials
To get started, please refer to our [Onboarding Materials](https://github.com/selego/whitepaper/blob/main/onboarding/Expectations.md) for essential guidelines and resources.

### Why It's Important to Converge to the Same Tech
- **Consistency:** Code should look like it was written by a single person.
- **Quality:** It's better to be consistently BAD than non-consistently GOOD.
- **Adaptation:** If you disagree with something in the whitepaper, write a message in [#whitepaper](https://slack.com/app_redirect?channel=C06Q7TFKTV0) channel in slack. 

### Audience
All Selego developers working on 0 to 1 projects or scale-ups.

### Scope
This guide covers repository structure, branching strategy, commit messages, pull requests, code reviews, coding standards, issue tracking, documentation, automation, security, and best practices, specifically tailored for MERN stack, React Native. 

## Table of Contents

1. [Javascript](#1-javascript)
   - 1.1 [Code Readiness](#11-code-readiness)
     - 1.1.1 [Early Returns](#111-early-returns)
     - 1.1.2 [Easy Confirmation](#112-easy-confirmation)
     - 1.1.3 [Update After an Action](#113-update-after-an-action)
     - 1.1.4 [Make It Easy for Others](#114-make-it-easy-for-others)
   - 1.2 [Beginner Mistakes we see way to often](#12-beginner-mistakes-we-see-way-to-often)
     - 1.2.1 [Filters in the frontend](#121-filters-in-the-frontend)
     - 1.2.2 [Update values](#122-update-values)
     - 1.2.3 [Abstractions: To Abstract or Not to Abstract?](#123-abstractions-to-abstract-or-not-to-abstract)
   - 1.3 [KIS: Keep it Simple](#13-kis-keep-it-simple)
     - 1.3.1 [What is KIS](#131-what-is-kis)
     - 1.3.2 [What is not KIS](#111-early-returns)
     - 1.3.3 [Why KIS](#133-why-kis)
   - 1.4 [Complexity](#14-complexity)
2. [Back-end](#2-back-end)
   - 2.1 [The Post Search](#21-the-post-search)
   - 2.2 [Respect 1 Post Route, 1 Object Created](#22-respect-1-post-route-1-object-created)
   - 2.3 [Consistency in Route Naming](#23-consistency-in-route-naming)
   - 2.4 [Flat Data vs Nested Data in MongoDB](#24-flat-data-vs-nested-data-in-mongodb)
   - 2.5 [Consistent API Responses ({data} object)](#25-consistent-api-responses-data-object)
   - 2.6 [Error Management Best Practices](#26-error-management-best-practices)
   - 2.7 [Simplifying API Endpoints](#27-simplifying-api-endpoints)
   - 2.8 [Separation of Concerns in Service Files](#28-separation-of-concerns-in-service-files)
3. [Front-end](#3-front-end)
   - 3.1 [Conventions on Calling API](#31-handling-api-responses)
   - 3.2 [Separating Concerns](#32-separating-concerns)
   - 3.3 [Avoid Partial Data Extraction](#33-avoid-partial-data-extraction)
   - 3.4 [Component Scoping](#34-component-scoping)
   - 3.5 [Streamlining API Calls](#35-streamlining-api-calls)
4. [DevOps](#4-devops)
5. [NoCode](#5-nocode)
6. [Project](#6-project)
   - 6.1 [Architecture](#61-architecture)
   - 6.2 [Validation?](#62-usage-of-joi-in-early-phases)
   - 6.3 [Uploading Files](#63-how-to-upload-files)
   - 6.4 [Domain Scoping](#64-domain-scoping)
   - 6.5 [Mono repo](#65-monorepo-approach)
   - 6.6 [Best Practices for Starting a Project](#66-best-practices-for-starting-a-project)
   - 6.7 [Service Code Approaches](#67-service-code-approaches)
   - 6.7 [Create Small PRs](#68-create-small-prs)


## 1. Javascript

### 1.1. Code Readiness

### 1.1.1. Early returns


Implementing early returns in your code can significantly enhance readability and maintainability. Instead of nesting logic within conditional blocks, an early return can simplify the structure of your functions by handling edge cases upfront and allowing the core logic to be more linear and easier to follow. Consider the following example:

#### ✖️ How to not do it:

```js
const { data, ok } = await api.get(`/meeting/${id}`);
if (ok) {
  console.log("Wow, now I have data, I'm going to set it.");
  setMeeting(data);
  ...
}
```

#### ✅ How to Do it

This code can be made more readable by applying an early return:

```js

const { data, ok } = await api.get(`/meeting/${id}`);
if (!ok) return;

console.log("Wow, now I have data, I'm going to set it.");
setMeeting(data);

...
```

#### ❓ Why to do this
By immediately returning when the condition is not met, the main logic is not indented and the function becomes more straightforward to understand.

### 1.1.2. Easy confirmation

A cheap way to get a confirmation behavior on deletion or important tasks. The only way to do it on MVPS:

```js
async function onDelete(){
  if (!window.confirm("Are you sure ?")) return;
  await submit();
}
```

### 1.1.3. Update After an Action
Effective state management is crucial when performing actions like creating, updating, or deleting data. Updating the state directly after these actions helps maintain UI consistency with the server's data.

#### ✖️ How to not do it
Here’s a suboptimal approach for handling a delete action that some devs often use after a delete:
```js
// Delete product
async function handleDelete(id) {
  if (!window.confirm("Are you sure?")) return;
  await api.remove(`/product/${id}`);
  toast.success("Successfully removed");
  setProducts(products.filter((product) => product.id !== id)); // 🚫 Manual state update
}
```
#### ❓ Why to not do this
Manually updating state after an action can be tedious and error-prone, leading to complexity and mismatches between the UI and server data. It makes the code harder to maintain and understand.

#### ✅ How to Do it
A more maintainable approach is to refresh the data after the action is completed. This ensures that the state remains consistent with the server's data:

```js
// Delete product
async function handleDelete(id) {
  if (!window.confirm("Are you sure?")) return;
  await api.remove(`/product/${id}`);
  toast.success("Successfully removed");
  fetch(); // ✅ Refresh data
}
```
#### ❓ Why to do this
- **Simplicity**: Using fetch() to refresh data reduces manual state management, making the code cleaner.
- **Consistency**: Ensures that the application state is always in sync with the server.
- **Maintainability**: Reduces the risk of bugs and simplifies future updates.

### 1.1.4 Make It Easy for Others

In a team setting, where multiple developers frequently jump in and out of projects, it's crucial to write code that's easy for others to understand. Every small detail can either boost collaboration or slow it down.

#### ✖️ How to not do it
```javascript
const getOrdersToPrepare = async (req) => {
  return new Promise((resolve, reject) => {
    odoo.connect(async function (err) {
      if (err) return console.log(err);
      let params = [];
      let date = getPreviousDay();
      let nextDay = getNextDay();
      if (req.query.date) date = req.query.date;
      params.push([[["commitment_date", ">=", getTodayDay()], ["commitment_date", "<", nextDay]]]);
    });
  });
};
```
- **Let's Break It Down:**
  - **Function Name (`getOrdersToPrepare`)**: Vague and doesn't fully convey the function's purpose.
  - **Parameter (`req`)**: Suggests that the function is tightly coupled with a request object, which may not always be necessary.
  - **Logic Flow**: The function is complex and mixes concerns (e.g., dealing with requests, dates, and Odoo connection) without clear separation, making it harder to follow.
  - **Date Handling**: The logic for handling dates is embedded within the function, which complicates the overall readability.

#### ✅ How to Do it
```javascript
const getOrdersToPrepareByDate = async (date = null) => {
  return new Promise((resolve, reject) => {
    odoo.connect(async function (err) {
      if (err) return console.log(err);
      let params = [];
      let selectedDate = date ? date : getPreviousDay();
      let nextDay = getNextDay();
      params.push([[["commitment_date", ">=", selectedDate], ["commitment_date", "<", nextDay]]]);
    });
  });
};
```
- **Let's Break It Down:**
  - **Function Name (`getOrdersToPrepareByDate`)**: Clear and descriptive, indicating that the function prepares orders based on a specific date.
  - **Parameter (`date`)**: Explicitly handles date input, making the function easier to understand and more flexible.
  - **Date Handling (`selectedDate`)**: The handling of the date is clearer and more straightforward, improving readability and maintainability.

### 1.2. Beginner Mistakes we see way to often

### 1.2.1 Filters in the frontend

#### ✖️ How to not do it:

```js
const {data, ok} = await api.get("/meeting");
setMeetings(response.data.filter((e) => !e.isDeleted));
```

#### ❓ Why to not do this

- because it will fuck up your pagination
- You will never know how many items you have
- It will not scale


#### ✅ How to Do it

```js
const {data, ok} = await api.post("/meeting/search",{deleted:false });
setMeetings(response.data);

```

```js
const query = {};
query.isDeleted = false;
const data = await MeetingModel.find(query).sort({ createdAt: -1 });
```
### 1.2.2 Update Values
#### ✖️ How to Not Do It
```javascript
const language = window.localStorage.getItem("i18nextLng");
await api.post("/api/users/language", { language });
```
```js
router.put("/:id", passport.authenticate(["admin", "user"], { session: false }), async (req, res) => {
  try {
    const data = await Company.findOneAndUpdate({ _id: req.params.id }, req.body);
    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});
```

#### ❓ Why to Not Do This
- **Incorrect Method for Updates**: POST should be used for creating new resources, not for updating existing ones. PUT is more appropriate for updating specific values.
- **Route Multiplication**: Creating a specific route for updating a field can lead to route Multiplication and is less scalable.
- **Security Issue**: Directly injecting the body for updates can expose your application to security vulnerabilities, allowing unintended modifications.

#### ✅ How to Do It
Update values correctly using the appropriate HTTP method. Instead of using POST for updates, use PUT to modify a specific resource:
```javascript
const language = window.localStorage.getItem("i18nextLng");
await api.put(`/api/users/${id}`, { language });
```
Add logical controls to ensure security. For example, to fetch only the object within the user’s organization, use this approach:
```javascript
router.get("/:id", passport.authenticate(["user"], { session: false }), async (req, res) => {
  try {
    const query = {
      organisationId: req.user.organisationId,
      _id: req.params.id
    };

    const data = await MissionObject.findOne(query);
    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});
```
[Here](https://www.imperva.com/learn/application-security/nosql-injection/) you can find an article that explain what can be done if you don’t

The same approach can be used for managing a list of content such as comments, tags, etc.

#### ✖️ How to Not Do It

```javascript
const addComment = async (todoId, comment) => {
    try {
      const { ok, data } = await api.post(`/mission_ao_todo/${todoId}/comment`, {
        text: comment,
        user_id: user._id,
        user_name: user.name,
        user_avatar: user.avatar,
      });
      if (!ok) throw new Error("Failed to add comment");

      setSelectedTodo(data);
      toast.success("Comment added successfully!");
    } catch (error) {
      console.error(error);
      toast.error("Error adding comment");
    }
  };
```

#### ❓ Why to Not Do This
- **Creates Unnecessary Routes**: Avoid adding dedicated endpoints for managing comments.
- **Increased Complexity**: Having separate routes for adding comments makes API maintenance harder.
- **Scalability Issues**: The approach is not optimal for large-scale applications.

#### ✅ How to Do It

```javascript
const addComment = async (todoId, comment) => {
  try {
    comments.push({
      text: comment,
      user_id: user._id, 
      user_name: user.name,
      user_avatar: user.avatar,
    });

    const { ok, data } = await api.put(`/mission_ao_todo/${todoId}`, { comments });
    if (!ok) throw new Error("Failed to add comment");

    setSelectedTodo(data);
    toast.success("Comment added successfully!");
  } catch (error) {
    console.error(error);
    toast.error("Error adding comment");
  }
};
```

#### ❓ Why This Approach?
- **Avoids Route Multiplication**: No need for a separate `/comment` endpoint.
- **Consistent Data Structure**: Keeps the comment list within the main object.
- **Easier Frontend Handling**: Always updating the entire object ensures that the UI stays in sync.

### 🔥 Key Takeaways
- Always use `PUT` for updates instead of `POST`.
- Avoid route multiplication to keep APIs clean and scalable.
- For comments, return all comments in the parent object rather than creating a separate endpoint.

By following these best practices, your application will be more maintainable, secure, and scalable.



### 1.2.3 Abstractions: To Abstract or Not to Abstract?

Abstractions are like that tricky magic trick: you do it when you don’t want to repeat yourself (DRY - Don’t Repeat Yourself). The DRY principle suggests that if you’re writing the same code twice, you should abstract it. 🛑 But hold up! Before you jump into abstraction, let’s weigh the pros and cons.

#### ✅ Pros of Abstraction:
- **Centralized Code:** Easier to maintain since all related code lives in one spot.
- **Hidden Complexity:** Sometimes you don’t need to know how something works, just that it works. Like using `getBoundingClientRect()`—you don’t care how it calculates, you just want the result.
- **Consistency:** Abstracting critical functions reduces the risk of bugs—no need to update the same logic in multiple places.

#### ✖️ Cons of Abstraction:
- **Readability:** It’s often easier to understand code when you can see it all in one place, without jumping between files.
- **Pain for Newcomers:** Abstracting simple tasks can make life harder for those new to the codebase.

#### Conclusion:
If you’re hesitating about whether to abstract something… you probably shouldn’t. At least, not yet.

#### ❌ Bad Abstraction Example: CRUD Operations

Abstraction gone wrong often looks like this:

Instead of writing:

```javascript
const {ok, data, error} = await API.get({ path: '/action' });
if (!ok) return alert(error);
setActions(data);
```

You might be tempted to abstract it:

```javascript
const getActions = () => API.get({ path: '/action' });

// Later in your code
const actions = await getActions();
setActions(actions.data);
```

**Why this is bad:**
- **Harder to Debug:** It’s not immediately clear what’s happening behind `getActions()`.
- **Unnecessary Complexity:** This abstraction doesn’t simplify anything, and it makes the flow harder to follow.

#### Real-Life Example

**Case:** In a project like Mano, where data is encrypted end-to-end, some backend tasks like creating update records happen on the front-end. The code is shared between a web dashboard and an Android app, and they share some code.

- **Pro Abstraction:** Shared code means less maintenance.
- **Con Abstraction:** It complicates the codebase and makes it harder for newcomers to understand.

**Decision:** In this scenario, they chose not to abstract the CRUD operations and updates. The reasoning? The code doesn’t change often, and it’s easier to read without abstraction.

#### ✨ Takeaway:
Abstraction is a powerful tool, but it’s not always the right answer. Think before you abstract — Remember [Keep it Simple 😉](#13-kis-keep-it-simple).

### 1.3 KIS: Keep it Simple
### 1.3.1 What is KIS
If you have an hour to solve a problem, spend 55 minutes thinking about it and 5 minutes on the solution. It's essential to consider various technical solutions before coding. Choose the simplest one, as it can save time, reduce costs, and make it easier for others to contribute especially for junior devs.

#### ✖️ How to not do it:
```js
import React, { useReducer, useMemo, useCallback } from 'react';

// Actions
const INCREMENT = 'INCREMENT';

// Reducer Function
const counterReducer = (state, action) => {
  switch (action.type) {
    case INCREMENT:
      return { count: state.count + 1 };
    default:
      throw new Error('Unknown action type');
  }
};

// Complex Counter Component
const ComplexCounter = () => {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  const increment = useCallback(() => dispatch({ type: INCREMENT }), []);
  const memoizedCount = useMemo(() => state.count, [state.count]);

  useEffect(() => {
    document.title = `Count: ${memoizedCount}`;
  }, [memoizedCount]);

  return (
    <div>
      <h1>Count: {memoizedCount}</h1>
      <button onClick={increment}>Increment</button>
    </div>
  );
};

export default ComplexCounter;
```
"As a junior dev, if I saw this code, I'd probably 🏃‍♂️ run away and never look back! 😅"

#### ✅ How to Do it
```js
import React, { useState } from 'react';

// Simple Counter Component
const Counter = () => {
  const [count, setCount] = useState(0);

  const increment = () => setCount(count + 1);

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={increment}>Increment</button>
    </div>
  );
};

export default Counter;
```
Other Examples of Keeping It Simple:
- **Use One Environment**: Stick with a single environment (e.g., production) as long as possible to avoid unnecessary complexity.
- **Stick with Basic Hooks**: Use useState and useEffect for most of your React needs. Avoid advanced hooks like useMemo and useReducer unless absolutely necessary.
- **Push Your Environment**: While not ideal for open-source projects, pushing your environment configurations can save time.
- **Security in Backlog**: Don’t focus on security from day one. Add it to the backlog and address it later.


### 1.3.2 What is not KIS
Keep it Simple does not mean making your code dirty. Simple code can be clean, readable, maintainable, and scalable. It’s about delivering "good enough" solutions quickly rather than aiming for perfection from the start.

#### 🔑 Key Points:
- **Simple ≠ Dirty**: Simple code should still be clean and well-structured.
- **Good Enough**: Sometimes implementing 60%-80% of a feature is sufficient.
- **Simple ≠ Easy**: Simple solutions might still be hard to implement.

### 1.3.3 Why KIS?
Keeping things simple helps us quickly develop MVPs (Minimum Viable Products), tailor them to user needs, and find Product-Market Fit (PMF) faster.

#### Reasons to Keep It Simple:
- **Speed**: Roll out products quickly (e.g., within 2 weeks).
- **Cost-Efficiency**: Avoid wasting money and time by not over-engineering solutions.
- **Adaptability**: Allows for pivots and changes without significant rework.

#### Traps to Avoid:
- **Quick but Dirty**: Avoid rushing tasks in a way that leads to technical debt and loss of credibility.
- **Identify Priorities**: Focus on critical parts of the application to ensure they are simple and scalable.
- **Avoid Over-Engineering**: Don’t add unnecessary features or complexity that may never be used.

#### Tips:
- **Stuck on a Task?**: If you’re working on something for over 30 minutes with no progress, reassess or ask for help.
- **Issue with a Feature**?: Look for existing solutions or get a second opinion on your approach.

#### Example:
A project rushed without considering user experience or code quality can lead to significant rework, wasting time and resources. Aim for a balance between speed and quality.

By focusing on these principles, you ensure that your solutions are both effective and maintainable.


### 1.4 Complexity  
When diving into the codebase of any project, there are certain 🚩 red flags—symptoms of complexity and technical debt—that you should be aware of. These symptoms indicate that the project might be more complex than it needs to be, and addressing them early can save a lot of headaches later on.

I'm going to introduce you to the three main symptoms of complexity and technical debt. After learning about them, you will start noticing them while working on a project. They're signs that the project you're working on is likely complex, and it's crucial to find a way to address them.

#### Symptom 1: The Unknown Unknowns  
The "Unknown Unknowns" are those pesky, unforeseen problems that pop up when you least expect them. These are the issues you didn’t see coming, and they can throw a wrench in your project if not handled properly.

##### ✖️ How Not to Do It  
1. Here’s what happens when you don’t anticipate the unknowns:
```js
const fetchData = async () => {
  const { data } = await api.get("/someEndpoint");
  setData(data);
};
```
- **What's wrong?** If something goes wrong during the API call, you won’t even know! There’s no error handling, and this can lead to unexpected crashes.

##### ✅ How to Do It  
2. Here's how to gracefully handle those surprises:
```js
const fetchData = async () => {
  try {
    const { data } = await api.get("/someEndpoint");
    setData(data);
  } catch (error) {
    setError(new Error('Failed to fetch data'));
  }
};
```
- **What's right?** Now, you’re prepared! If the API call fails, the error is caught and managed, ensuring your app doesn’t just crash unexpectedly.

#### Symptom 2: Cognitive Load  
Cognitive load is the mental effort required to understand your code. The simpler and more straightforward your code is, the easier it is for others (and future you) to understand and maintain it.

##### ✖️ How Not to Do It  
Example 1: Too much complexity can make your brain hurt!
```js
import React, { useState, useEffect, useCallback } from 'react';

function UserList({ getUsersFromServer }) {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  const fetchUsers = useCallback(async () => {
    try {
      setLoading(true);
      const fetchedUsers = await getUsersFromServer();
      setUsers(fetchedUsers);
    } catch (e) {
      setError(e.message);
    } finally {
      setLoading(false);
    }
  }, [getUsersFromServer]);

  useEffect(() => {
    fetchUsers();
  }, [fetchUsers]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <ul>
      {users.map((user, index) => (
        <li key={index}>{user.name}</li>
      ))}
    </ul>
  );
}

export default UserList;
```
- **What's wrong?** There’s too much going on! The use of `useCallback`, `useEffect`, and multiple state variables adds unnecessary complexity, making it harder for others to quickly grasp what’s happening.

##### ✅ How to Do It  
Example 2: Keep it simple, keep it smart!
```js
import React, { useState } from 'react';

function UserList({ initialUsers }) {
  const [users, setUsers] = useState(initialUsers);

  return (
    <ul>
      {users.map((user, index) => (
        <li key={index}>{user.name}</li>
      ))}
    </ul>
  );
}

export default UserList;
```
- **What's right?** By simplifying the component, the cognitive load is reduced. It’s now easier to understand, maintain, and extend if necessary.

#### Symptom 3: Change Amplification  
Change amplification happens when a tiny change in one area forces you to modify other unrelated parts of the system. This is a major headache when maintaining or updating software.

Certainly! Let’s illustrate the scenario where having different props for similar UI elements leads to increased complexity due to abstraction. We'll use the Tailwind CSS input component as an example.

##### ✖️ How to Not Do It
**Scenario: Highly Abstracted Component with Many Conditional Props**

In this example, the input component is highly abstracted to handle various configurations. However, as different components require different props, the complexity of the abstraction grows:

```jsx
// Complex abstracted input component with many conditional props
const Input = ({
  name,
  prefix,
  defaultValue,
  label,
  placeholder,
  isDisabled = false,
  isRequired = false,
  type = 'text',
  onChange,
  onFocus,
  onBlur,
  ...props
}) => {
  return (
    <div className="relative">
      {label && (
        <label htmlFor={name} className="block text-sm font-medium text-gray-700">
          {label}
        </label>
      )}
      {prefix && (
        <span className="absolute inset-y-0 left-0 flex items-center pl-3">
          <span className="text-gray-500">{prefix}</span>
        </span>
      )}
      <input
        id={name}
        name={name}
        type={type}
        defaultValue={defaultValue}
        placeholder={placeholder}
        disabled={isDisabled}
        required={isRequired}
        onChange={onChange}
        onFocus={onFocus}
        onBlur={onBlur}
        className={`block w-full pl-10 pr-3 py-2 border rounded-md ${isDisabled ? 'bg-gray-100 cursor-not-allowed' : 'bg-white'}`}
        {...props}
      />
    </div>
  );
};

// Usage in Component A and Component B with different props
const ComponentA = () => {
  return (
    <Input
      name="username"
      label="Username"
      defaultValue="User123"
      isDisabled={false}
      onChange={(e) => console.log(e.target.value)}
    />
  );
};

const ComponentB = () => {
  return (
    <Input
      name="email"
      label="Email Address"
      prefix="📧"
      placeholder="Enter your email"
      isRequired={true}
      onFocus={() => console.log('Focused')}
    />
  );
};
```

In this abstracted example, the `Input` component is designed to accommodate various props. However, the more conditional logic and props you add, the more complex and harder to maintain it becomes, ⚠️ especially when different components need different configurations.

##### ✅ How to Do It
**Scenario: Using Plain JSX/HTML Elements for Specific Cases**

Instead of creating a single complex component, write simpler JSX/HTML elements tailored to each component’s specific needs. This approach reduces complexity and makes the code easier to manage:

```jsx
// Plain Input component for Component A
const ComponentA = () => {
  return (
    <div className="relative">
      <label htmlFor="username" className="block text-sm font-medium text-gray-700">
        Username
      </label>
      <input
        id="username"
        name="username"
        type="text"
        defaultValue="User123"
        className="block w-full py-2 border rounded-md bg-white"
        onChange={(e) => console.log(e.target.value)}
      />
    </div>
  );
};

// Plain Input component for Component B
const ComponentB = () => {
  return (
    <div className="relative">
      <label htmlFor="email" className="block text-sm font-medium text-gray-700">
        Email Address
      </label>
      <div className="absolute inset-y-0 left-0 flex items-center pl-3">
        <span className="text-gray-500">📧</span>
      </div>
      <input
        id="email"
        name="email"
        type="email"
        placeholder="Enter your email"
        required
        className="block w-full pl-10 py-2 border rounded-md bg-white"
        onFocus={() => console.log('Focused')}
      />
    </div>
  );
};
```
In this simplified approach, each component directly uses plain JSX/HTML elements suited to its specific needs. This avoids the complexity of a highly abstracted component, making each component’s code more straightforward and easier to maintain. You handle each case directly without adding unnecessary abstraction or conditional logic.

#### Conclusion  
Complexity in code isn’t just about the number of lines—it’s about how understandable, maintainable, and stable that code is. By recognizing these symptoms—Unknown Unknowns, Cognitive Load, and Change Amplification—you can steer your project away from the pitfalls of complexity and toward clean, efficient, and enjoyable coding practices. Keep it simple, and your future self (and your teammates) will thank you!


## 2. Back-end

### 2.1. The Post Search

Here is the method we use to fetch lists. In most cases, we need to perform extensive filtering on these lists, and a classical GET request can become really messy due to the querying. Instead, we use a POST request for more flexible querying.

#### ✅ How to Do it

```js
router.post("/search", async (req, res) => {
  try {
    const query = {};
    if (req.body.user_id) query.user_id = req.body.user_id;
    if (req.body.device_type) query.device_type = req.body.device_type;
    if (req.body.status) query.status = req.body.status;

    const data = await DeviceModel.find(query);
    return res.status(200).send({ ok: true, data });

  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});
```

#### ❓ Why to not do this
By using a POST request, we can send a complex object in the body of the request, which allows us to construct more flexible and powerful queries.

### 2.2. Respect 1 Post Route, 1 Object Created

This helps us separating concerns and simplifying the API's behavior. This approach ensures that each endpoint has a single responsibility, making the API more predictable, easier to copy and easier to maintain.

Checkout this example where a single POST request is used to create multiple objects such as an announcement, a company, and more. This can quickly lead to a bloated and complex endpoint, as seen in the following code snippet from a GitHub repository:

[View the code](https://github.com/selego/linkera/blob/main/api/src/controllers/annonce.js)

Adhering to the principle of "1 POST route, 1 object created" helps mitigate these issues by ensuring each route is responsible for a single task. This results in:
- **Simpler and Cleaner Code:** Each controller method remains focused on a single responsibility, making the code easier to read and maintain.
- **Improved Debugging:** With each route handling only one type of object, debugging becomes more straightforward.
- **Better Modularity:** Decoupled logic allows for easier modifications and enhancements in the future without affecting other parts of the code.


### 2.3. Consistency in Route Naming

Maintaining consistency in route naming conventions is essential for improving code readability and maintainability. One approach to achieving this is by avoiding the duplication of controller names within routes and using generic identifiers like `:id` instead. For example, instead of using both `dataRoomId` and `data_room_id`, simply using `:id` can reduce confusion and make the code easier to work with. This method not only simplifies the route definitions but also facilitates copying and pasting, as the placeholder is generic and applicable across different contexts.

#### ✖️ How to not do it:

Using specific names for different routes can lead to inconsistency and confusion.

```js
router.get("/dataRoom/:dataRoomId", passport.authenticate(["user"], { session: false }), async (req, res) => {
  try {
    const { dataRoomId } = req.params;
    // logic to handle dataRoom
  } catch (error) {
    res.status(500).send({ ok: false, error });
  }
});
```
#### ✅ How to Do it

Using a generic :id makes the routes cleaner and more maintainable.

```js
router.get("/dataRoom/:id", passport.authenticate(["user"], { session: false }), async (req, res) => {
  try {
    const { id } = req.params;
    // logic to handle dataRoom
  } catch (error) {
    res.status(500).send({ ok: false, error });
  }
});

```

### 2.4. Flat Data vs Nested Data in MongoDB
Imagine you have a task object and user object, a user can create a task and users (others) can apply to it. You can approach it with either a flat or nested structure. Here’s a comparison of both methods:

#### Nested Data Approach:
- Nested Data Structure:
```js
{
   name: String, 
   created_by_user_id: Object.id,
   applicant_users_ids: [Object.id]
}
```

- Getting Data:
```js
router.get("/:id", passport.authenticate(["admin", "user"], { session: false }), async (req, res) => {
  try {
    const task = await TaskModel.findOne({ _id: req.params.id });
    const createdByUser = await UserModel.findById(task.created_by_user_id);
    const applicantUsers = await UserModel.find({ _id: { $in: task.applicant_users_ids } });
    const data = { ...task._doc, createdByUser, applicantUsers };
    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});
```
- **Pros & Cons:**
  - **(medium impact)** Every property of the user will be returned.
  - **(medium impact)** It’s challenging to use tools like Sendinblue or Metabase.
  - **(low impact)** The data remains synchronized with the user. If you update the user’s name, it’s updated everywhere.
  - **(low impact)** Retrieving objects is slightly slower.
  - **(high impact)** It increases the complexity of the code.

#### Flat Data Approach:
- Flat Data Structure:
```js
{
   name: String, 
   created_by_user_id: String,
   created_by_user_name: String,
   created_by_user_avatar: String,
   applicant_users: [
       id: String,
       name: String,
       avatar: String
   ]
}
```

- Getting Data (Flat Approach):
```js
router.get("/:id", passport.authenticate(["admin", "user"], { session: false }), async (req, res) => {
  try {
    const data = await TaskModel.findOne({ _id: req.params.id });
    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});
```
- **Pros & Cons:**
  - **(high impact)** It decreases the complexity of the code.
  - **(medium impact)** Adding new properties to the user’s object can be tedious.
  - **(medium impact)** It’s easy to integrate with tools like Sendinblue or Metabase.
  - **(low impact)** The data is unsynchronized with the user, meaning the user’s name can only be changed through a script manually.
  - **(low impact)** Retrieving objects is faster.

#### Summary:
Flat structures offer simplicity and speed, making them easier to manage and faster to work with. However, they require careful management to keep data synchronized and consistent. On the other hand, nested structures are ideal when you need to maintain strong data consistency across the system but come with increased complexity




### 2.5. Consistent API Responses ({data} object)
We established a convention where every response returns a *{data}* object. This ensures that every API response follows a predictable structure, making it easier to handle responses and reducing the likelihood of unexpected issues.

#### ✅ How to Do it
```js
router.get("/:id", async (req, res) => {
  try {
    const data = await MissionObject.findOne({ _id: req.params.id });
    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});
```
### 2.6 Error Management Best Practices
#### ✖️ How to Not Do It:
Using plain text error messages in responses:
```js
if (!name) return res.status(409).send({ ok: false, error: "name missing" });
```
#### ✅ How to Do It:
Use standardized error codes or constants:
```js
if (!name) return res.status(409).send({ ok: false, error: "NAME_MISSING" });
```
#### ❓ Why to Do This:
Using standardized error codes like *"NAME_MISSING"* simplifies later tasks such as translation and code detection.

### 2.7 Simplifying API Endpoints

When designing APIs, it's crucial to minimize the number of endpoints while maximizing their functionality. This approach simplifies the backend code, making it easier to maintain and understand, and prevents endpoint Escalation. Here's an example that illustrates this principle by consolidating multiple operations into a single route, avoiding redundancy, and streamlining the update process.

#### ✖️ How to Not Do It

Creating multiple routes for similar operations increases the cognitive load and adds unnecessary complexity.

```js
// A dedicated route just for adding a campaign to a contact
router.put("/:id/addToCampaign", passport.authenticate(["admin", "user"], { session: false }), async (req, res) => {
  try {
    const data = await contactModel.findOne({ _id: req.params.id });
    if (!data) return res.status(400).send({ ok: false, code: "UNKNOWN_CONTACT" });
    if (!req.body?.campaignId || !req.body?.campaignName) return res.status(400).send({ ok: false, code: "MISSING_CAMPAIGN" });

    data.campaigns.push({ id: req.body.campaignId, name: req.body.campaignName });
    data.updated_at = new Date();
    await data.save();

    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});
```

#### ✅ How to Do It
Consolidating logic into a single route reduces complexity and makes the code more maintainable and reusable.

```js
// A single route handling multiple updates, including adding a campaign to a contact
router.put("/:id", passport.authenticate(["admin", "user"], { session: false }), async (req, res) => {
  try {
    const data = await contactModel.findOne({ _id: req.params.id });
    if (!data) return res.status(400).send({ ok: false, code: "UNKNOWN_CONTACT" });

    if (req.body?.campaignId) {
      data.campaigns.push({ id: req.body.campaignId, name: req.body.campaignName });
    }

    // Other potential updates handled here...

    await data.save();

    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});
```

#### ❓ Why to  do this
- **Efficiency**: By using a single endpoint to handle multiple related operations, we reduce the number of API routes, making the backend easier to maintain.
- **Flexibility**: The consolidated route can handle various updates based on the request payload, making it more versatile an reusable.
- **Simplification**: Reduces the need for redundant code and ensures that all related logic is in one place, making future updates simpler and less error-prone.

By following this approach, we avoid the pitfalls of endpoint escalation and keep our codebase clean and efficient, aligning with the principles of simplicity and maintainability.

#### Client-Side Management
Alternatively, you can manage the campaigns field directly from the client side:

```js
// CLIENT-SIDE EXAMPLE: Managing campaigns directly
const updatedCampaigns = [...knownUser.campaigns];
updatedCampaigns.push({ campaignName: selectedCampaign.name, campaignId: selectedCampaign._id });
await api.put(`s_contact/${knownUser._id}`, { campaigns: updatedCampaigns });
```
**Explanation**: `updatedCampaigns` is a copy of the existing campaigns array with the new campaign added. The aim is to manage the entire `knownUser.campaigns` from the client side, sending the complete updated list in one go. This simplifies the server-side logic and ensures that all updates are made in a single request.

### 2.8 Separation of Concerns in Service Files 

To ensure our codebase remains modular and reusable, service files should only include general-purpose functionality. They should not contain business logic or project-specific details. This ensures that service files can be copied and reused across different projects.

#### ✖️ How to Not Do It

```js
const socketIO = require("socket.io");
const Anthropic = require("@anthropic-ai/sdk");
const { CLAUDE_API_KEY } = require("../config");

const client = new Anthropic({ apiKey: CLAUDE_API_KEY });

// Business-specific logic embedded (NOT RECOMMENDED)
async function generateAIMessage(prompt) {
  const response = await client.complete({ prompt });
  return response.completion;
}

async function processSocketEvent(eventData) {
  if (!eventData.type) {
    throw new Error("Event type is missing.");
  }
  if (eventData.type === "chat") {
    return await generateAIMessage(eventData.message);
  } else if (eventData.type === "stream") {
    return await getStreamFromAI(eventData.input);
  }
}

module.exports = { client, generateAIMessage, processSocketEvent };
```

The inclusion of functions like generateAIMessage, processSocketEvent ties the service file to the current project’s business logic. This mixes responsibilities, making the service file harder to maintain and less reusable.

#### ✅ How to Do It

```js
const socketIO = require("socket.io");
const Anthropic = require("@anthropic-ai/sdk");
const { CLAUDE_API_KEY } = require("../config");

const client = new Anthropic({ apiKey: CLAUDE_API_KEY });

module.exports = { client };
```

This example show a service file designed for reusability. It only initializes and exports a general-purpose client without embedding project-specific logic. Business logic and project-specific functions should be written in either controllers or utility files to maintain a clear separation of concerns.

#### ❓ Why to  do this
- **Reusability**: By keeping service files generic and free from project-specific logic, they can be easily reused across different projects, reducing duplication of code and effort.
- **Simplification**: This separation simplifies the codebase, making it easier to understand, maintain, and debug, as each component has a clearly defined purpose.

#### ✖️ Another Issue That's Seen a Lot: Separating Initialization from Controllers

Another common issue is the direct initialization of services within controllers. This practice can clutter controller files and make it hard to maintain. Instead, it's good to extract initialization logic into service files, allowing controllers to focus solely on handling requests and responses.

#### ✖️ How to Not Do It

Here's an example of a controller that improperly initializes a service directly within the controller:

```js
/// .... imports 

const AgentObject = require("../models/agent");
const { OPENAI_API_KEY } = require("../config");

const openai = new OpenAI({ apiKey: OPENAI_API_KEY }); // Directly initializing the client here

const SERVER_ERROR = "SERVER_ERROR";
const BAD_REQUEST = "BAD_REQUEST";

router.post("/", passport.authenticate(["admin", "user"], { session: false }), async (req, res) => {
  try {
    if (!req.body.name) return res.status(400).send({ ok: false, code: BAD_REQUEST });
    
    // Use the openai client directly in the controller...
    const response = await openai.chat.completions.create({ /* parameters */ });
    
    const data = await AgentObject.create(req.body);
    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});

```

#### ✅ How to Do It

Instead, the initialization logic should be moved to its own service file:

```javascript

// ... imports

const AgentObject = require("../models/agent");
const openai = require("../services/openai"); // Import the initialized client service 

const SERVER_ERROR = "SERVER_ERROR";
const BAD_REQUEST = "BAD_REQUEST";

router.post("/", passport.authenticate(["admin", "user"], { session: false }), async (req, res) => {
  try {
    if (!req.body.name) return res.status(400).send({ ok: false, code: BAD_REQUEST });
    
    // Use the imported openai client
    const response = await openai.chat.completions.create({ /* parameters */ });
    
    const data = await AgentObject.create(req.body);
    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});

```

By adopting this approach, you keep the controller focused on its primary responsibility, leading to cleaner and more maintainable code.

## 3. Front-end

### 3.1. Handling API Responses:

#### ✖️ How to not do it:

```javascript
useEffect(() => {
  try {
    api.post("/event/search").then(({ data, ok }) => {
      if (!ok) return toast.error(data.message);
      setEvents(data);
      setTotal(data.total);
      setLoading(false);
    });
  } catch (e) {
    console.error(e);
  }
}, []);
```
#### ✅ How to Do it

```javascript
const fetchEvents = async () => {
  try {
    const { data, ok, total } = await api.post("/event/search");
    if (!ok) return toast.error(data.message);
    setEvents(data);
    setTotal(total);
  } catch (e) {
    console.error(e);
  } finally {
    setLoading(false);
  }
};

useEffect(() => {
  fetchEvents();
}, []);
```


### 3.2. Separating Concerns

When fetching data, it's crucial to maintain a clear separation of concerns. If you’re fetching an `annonce`, focus solely on fetching the `annonce`. Fetching additional data, such as a company, at the same time complicates the controller and the call itself. This approach introduces inconsistency in the data object returned from the controller.

Here’s an example of how this problem manifests:

#### ✖️ How to not do it:

```js
useEffect(() => {
  const fetchData = async () => {
    try {
      const res = await api.get(`/annonce/${annonceId}`);
      if (res.ok) {
        setAnnonce(res.data.annonce);
        setCompany(res.data.company);
      } else {
        toast.error("Une erreur est survenue");
      }
    } catch (e) {
      console.error(e);
    }
  };
  fetchData();
}, [annonceId]);
```

#### ❓ Why to not do this

1. **Complex Controller Logic:** Fetching both `annonce` and `company` in the same call increases the complexity of the controller.
2. **Inconsistent Data Object:** The returned data object from the controller may contain inconsistent structures.
3. **Lack of Early Return:** The absence of early return statements reduces code readability.
4. **No Destructuring:** Not using `{ data, ok }` destructuring leads to less clean and more error-prone code.

#### ✅ How to Do it

Separate the fetching logic to maintain clarity and consistency, with functions defined outside of `useEffect`:

```js
const fetchAnnonce = async (annonceId) => {
  try {
    const { ok, data } = await api.get(`/annonce/${annonceId}`);
    if (!ok) return toast.error("Une erreur est survenue");
    setAnnonce(data.annonce);
  } catch (e) {
    console.error(e);
  }
};

const fetchCompany = async (companyId) => {
  try {
    const { ok, data } = await api.get(`/company/${companyId}`);
    if (!ok) return toast.error("Une erreur est survenue");
    setCompany(data.company);
  } catch (e) {
    console.error(e);
  }
};

useEffect(() => {
  fetchAnnonce(annonceId);
  fetchCompany(companyId);
}, [annonceId, companyId]);
```

#### ❓ Why to  do this

1. **Clear Separation of Concerns:** Each function is responsible for fetching a specific piece of data.
2. **Simplified Controller Logic:** The controller remains simple and focused.
3. **Consistent Data Handling:** Each data object is handled independently, maintaining consistency.
4. **Early Returns and Destructuring:** Using early returns and destructuring improves code readability and reduces potential errors.


### 3.3. Avoid Partial Data Extraction

When fetching data from an API, resist the temptation to extract and store only part of the response object, like a specific field. This can lead to issues when scaling or updating your application. Instead, store the entire object, which ensures future flexibility and easier code maintenance.

#### ✖️ How to not do it:

```javascript
const getInvitations = async () => {
  const { data, ok } = await api.post('/membership_event/search', {
    event_id: id
  });

  if (!ok) {
    toast.error('Error loading invitations');
    return;
  }

  // Only storing a single field (user_id) from the data
  setInvitations(data.map(membership => membership.user_id));
};
```

#### ✅ How to Do it

Instead of extracting specific fields, store the entire object:

```javascript
const getInvitations = async () => {
  const { data, ok } = await api.post('/membership_event/search', {
    event_id: id
  });

  if (!ok) {
    toast.error('Error loading invitations');
    return;
  }

  // Store the full data object
  setInvitations(data);
};

```

#### ❓ Why to do this
- Storing only a subset of the response may lead to refactoring when additional fields are needed.
- Storing the complete data object provides more flexibility for future changes.
- This approach scales better as the application grows and requirements evolve.


```js

```

### 3.4 Component Scoping

Component scoping, which is the practice of defining clear boundaries for each component's responsibilities, when done wrong, will increase complexity (Change Amplification). Poorly scoped components can lead to tangled logic and harder maintenance. 


#### ✖️ How to not do it:
Here is an example of a wrong component scoping often seen. The page is composed of a list of cards “meetings” and a modal to bind your account.
Take your time, it's not easy to read.
```js
export default function Meetings() {
  const [meetings, setMeetings] = useState([]);
  const [openModal, setOpenModal] = useState(false);
  const [value, setValue] = useState('');

  useEffect(() => {
    fetchMeetings();
  }, []);

  async function fetchMeetings() {
    // API call to get meetings data and set state
  }

  const onSaveUrl = async (val) => {
    // API call to save URL and handle response
  };

  const meetingsList = meetings.map((meeting) => (
    <div key={meeting._id} className="...">
      {/* Render each meeting card */}
    </div>
  ));

  return (
    <>
      <div className="relative p-6 bg-[#272727] text-white min-h-screen">
        {/* Render page header */}
        <button
          type="button"
          className="border hover:border-white hover:shadow-neon bg-red-800 ml-4 mb-10 rounded-lg px-3 py-2"
          onClick={() => setOpenModal(true)}
        >
          Bind your account
        </button>
        <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
          {meetingsList}
        </div>
      </div>
      <Modal isOpen={openModal} onClose={() => setOpenModal(false)}>
        <form>
          <input
            type="text"
            value={value}
            onChange={(e) => setValue(e.target.value)}
          />
          <button type="button" onClick={() => onSaveUrl(value)}>
            Save
          </button>
          <a
            href="https://accounting.selego.co/learn/655f367c33085306bd711304?index=4"
            target="_blank"
          >
            Watch the tuto
          </a>
        </form>
      </Modal>
    </>
  );
}

```

#### ❓ Why to not do this
The "Bind Your Account" feature consists of a field, a modal, and a save function. In the example above, these elements are scattered throughout the component, creating a disorganized and messy codebase. From a business perspective, it makes sense to isolate this feature into its own component, allowing for easier improvements or removal later on. In fact, we ended up deleting it just a day later.

#### ✅ How to Do it
Instead, isolate the "Bind Your Account" feature into its own component. This will make your code more modular, easier to maintain, and scalable for future changes. Here's how you could refactor the code:

```js
export default function Meetings() {
  const [meetings, setMeetings] = useState([]);

  useEffect(() => {
    fetchMeetings();
  }, []);

  async function fetchMeetings() {
    // API call to get meetings data and set state
  }

  return (
    <>
      <div className="relative p-6 bg-[#272727] text-white min-h-screen">
        {/* Render page header */}
        <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
          {meetings.map((meeting) => (
            <div key={meeting._id} className="...">
              {/* Render each meeting card */}
            </div>
          ))}
        </div>
      </div>
      <BindAccount />
    </>
  );
}

const BindAccount = () => {
  const [openModal, setOpenModal] = useState(false); // State to manage modal visibility
  const [value, setValue] = useState(''); // State for input value

  const onSaveUrl = async (val) => {
    // API call to save URL and handle response
  };

  return (
    <>
      <button
        type="button"
        className="border hover:border-white hover:shadow-neon bg-red-800 ml-4 mb-10 rounded-lg px-3 py-2"
        onClick={() => setOpenModal(true)}
      >
        Bind your account
      </button>
      <Modal isOpen={openModal} onClose={() => setOpenModal(false)}>
        <form>
          <input
            type="text"
            value={value}
            onChange={(e) => setValue(e.target.value)}
          />
          <button type="button" onClick={() => onSaveUrl(value)}>
            Save
          </button>
          <a
            href="https://accounting.selego.co/learn/655f367c33085306bd711304?index=4"
            target="_blank"
          >
            Watch the tuto
          </a>
        </form>
      </Modal>
    </>
  );
};
```
By refactoring your code this way, each feature is neatly scoped into its own component, making your codebase cleaner, more maintainable, and less prone to bugs as your application evolves.

### 3.5 Streamlining API Calls
#### ✖️ How to not do it:
You might see a separate business function for each API call in some projects, like this:

```js
export async function getCashbackSites(referralCode) {
  const response = await fetch('https://myproject.com/campaigns/extension/' + referralCode, {
    method: 'GET',
    headers: { 'language': 'fr_fr' },
  });
  if (response.status == 401) return null;
  const data = await response.json();
  return data.data;
}

export async function getReferral(code) {
  const response = await fetch('https://myproject.com/campaigns/referral/' + code, {
    method: 'GET',
    headers: { 'language': 'fr_fr' },
  });
  if (response.status == 401) return null;
  const data = await response.json();
  return data.data;
}
```
#### ❓ Why to not do this:
This approach can lead to a multiplication of functions, each doing a similar task but for different endpoints. Over time, this can make the codebase harder to manage and understand. When calling a function like getCashbackSites, you may not know exactly what it’s doing without digging into the implementation. This creates unnecessary complexity and an extra layer of abstraction that doesn't add real value.

#### ✅ How to do it:
Instead, we need to remove this abstraction level as much as possible and directly use our clean routes.
This way, business is on the proper page and api is just a service without business inside

```js
class ApiService {
  async get(path) {
    try {
      const response = await fetch(`${apiURL}${path}`, {
        method: 'GET',
        headers: { 'Content-Type': 'application/json', Authorization: `JWT ${this.token}` },
      });
      return await response.json();
    } catch (error) {
      throw error;
    }
  }
}
const api = new ApiService();
```

Use it directly like this:
```js
api.get(`/affiliates/campaigns/extension/${referralCode}`);
```
For further guidance, check out the **complexity tutorial**.


## 4. DevOps

## 5. NoCode

## 6. Project

### 6.1. Architecture

We try to keep the same project architecture because:
- It is much easier to jump from one project to another within the company.
- We try to keep code look like it's written from the same person, even througout projects, same for the way we structure our files.

```plaintext
project-root
│
├── api
│   ├── src
│   │   ├── models
│   │   │   └── (all data models)
│   │   │
│   │   ├── controllers
│   │   │   └── (all controllers) 
│   │   │
│   │   ├── services
│   │   │   └── (service-related files)
│   │   │
│   │   ├── utils
│   │   │     └── (utility files)
│   │   │
│   │   └── index.js
│   │
│   └── (other non-source files and configurations)
│   
├── app
│   ├── src
│   │   ├── components
│   │   │   └── (global components)
│   │   │
│   │   ├── scenes
│   │   │   ├── auth
│   │   │   │   └── (auth-related components and files)
│   │   │   │
│   │   │   └── home
│   │   │       ├── components
│   │   │       │   └── (home-related components)
│   │   │       │
│   │   │       └── index.js
│   │   │
│   │   ├── services
│   │   │   └── (service-related files)
│   │   │
│   │   └── utils
│   │       └── (utility files)
│   │
│   └── (other non-source files and configurations)
│
└── (other project-related files and configurations)

```


### 6.2. Usage of Joi in Early Phases

Using Joi in the early phases of a project, where frequent changes are common, is not optimal due to the added complexity and increased development time it introduces. The structured nature of Joi's validation hinders rapid iterations based on evolving requirements.

**Considerations:**
- Should it be used in the very early stages? Probably not, due to rapid changes.
- Does it slow down development in the early phases? Yes, it can.
- Is it necessary in a more mature phase of the project? Absolutely.

It unfortunately makes the code look too messy at the moment and reduces code readability. 
Here is an example of how using Joi can make the code complex and difficult to read. We changed the attributes of the model a few times and the checks were slowing us down. 

```js
function validateContact(contact) {
  return Joi.object({
    type: Joi.string().valid("INDIVIDUAL", "ASSOCIATION", "COMPANY").required(),
    firstname: Joi.string().allow(null, ""),
    lastname: Joi.string().allow(null, ""),
    email: Joi.string().allow(null, ""),
    phone: Joi.string().allow(null, ""),
    city: Joi.string().allow(null, ""),
    invitationStatus: Joi.string().valid("SENT", "NONE", "ERROR").required(),
    prioritized: Joi.boolean().required(),
    allow_information: Joi.boolean().allow(null, ""),
    adhesion: Joi.boolean().allow(null, ""),
    invitation_send_at: Joi.date().allow(null, ""),
    org_name: Joi.string().allow(null, ""),
    activity: Joi.string().allow(null, ""),
    nb_employee: Joi.number().allow(null, ""),
    tag_ids: Joi.array().items(Joi.string().uuid()).allow(null, ""),
    status: Joi.string()
      .valid("NEW", "FOLLOW_UP", "CONTACTED", "PLANNED", "MET", "CLOSED_WIN", "CLOSED_LOST")
      .required(),
    appointment_date: Joi.date().allow(null, ""),
    operator_id: Joi.string().allow(null, ""), // only for SUPER_ADMIN
  }).validate(contact, { stripUnknown: true });
}
```

### 6.3. How to Upload Files

Integrating the same file handler improves our efficiency. Please use the approach below.

#### ✅ How to Do it

##### Backend Counterpart

To streamline the process of uploading files such as photos, audio, video, or documents to our database, we typically use S3 buckets from CleverCloud. Instead of duplicating the same code across multiple repositories, we introduced a dedicated file handler. Here's an example of the improved approach:

```js
const express = require("express");
const router = express.Router();
const crypto = require("crypto");
const { uploadToS3FromBuffer } = require("../utils");

router.post("/", async (req, res) => {
  const { files, folder } = req.body;

  if (!folder) return res.status(400).send({ ok: false, message: "No folder specified" });
  if (!files) return res.status(400).send({ ok: false, message: "No files uploaded" });

  const filesArray = Array.isArray(files) ? files : [files];

  const uploadPromises = filesArray.map((file) => {
    const base64ContentArray = file.rawBody.split(",");
    const contentType = base64ContentArray[0].match(/[^:\s*]\w+\/[\w-+\d.]+(?=[;| ])/)[0];
    const extension = file.name.split(".").pop();
    const buffer = Buffer.from(base64ContentArray[1], "base64");
    const uuid = crypto.randomBytes(16).toString("hex");
    return uploadToS3FromBuffer(`file${folder}/${uuid}/${file.name}.${extension}`, buffer, contentType);
  });

  try {
    const urls = await Promise.all(uploadPromises);
    return res.status(200).send({ ok: true, data: urls });
  } catch (error) {
    console.error(error);
    return res.status(500).send({ ok: false, message: "Error in file upload" });
  }
});

module.exports = router;
```

With this file handler, you only need to copy-paste it into your controllers and adjust the necessary S3 keys for each new project. This handler processes the file upload and returns the URL(s), which you can then add to your model along with other data during create or update operations.

##### Frontend Counterpart

The duplication reduction extends to the frontend as well. By using a reusable file input component, you can streamline file uploads across different projects. Here's an example from our global components:

<a href="https://github.com/selego/matteriality/blob/main/app/src/components/file-input.jsx" target="_blank">File Input Component</a>

You can copy-paste this component into your project and adjust the styling to match your needs. This way, the file upload process is standardized and simplified, improving overall efficiency.

### 6.4 Domain Scoping

#### Introduction
In software development, domain scoping is about clearly defining and separating the different business objects and their related logic in your application. This helps avoid confusion and complexity as your project grows.

#### Example
Let's say you have an application for managing clients and suppliers. 

#### ✖️ How to not do it:
Avoid these practices to prevent messy and hard-to-maintain code:

1. **Using the Same Component for Multiple Routes:**
```js
const routes = (isLoggedIn, isAdmin) => [
  {
    path: "/clients/*",
    element: isLoggedIn ? <ContactList /> : <Navigate to="/auth" />,
  },
  {
    path: "/suppliers/*",
    element: isLoggedIn ? <ContactList /> : <Navigate to="/auth" />,
  },
];
   ```

2. **Switching Between Business Objects in the Same Component:**
```js
const List = () => {
  const [contacts, setContacts] = useState();
  const [selectedContact, setSelectedContact] = useState();
  const isClient = location.pathname.indexOf("/clients") !== -1;
  const type = isClient ? "client" : "supplier";
};
```

3. **Conditional Rendering for Different Business Objects:**
```js
const List = () => {
  const [contacts, setContacts] = useState();
  const [selectedContact, setSelectedContact] = useState();
  const isClient = location.pathname.indexOf("/clients") !== -1;
  const type = isClient ? "client" : "supplier";

  return (
    <div className="font-[Helvetica] text-center text-[24px] mb-4">
      Creating a {type}
    </div>
  );
};
```

4. **Bad architecture**
```
app
├── src
│   ├── scenes
│   │   ├── contacts
│   │   │   ├── createContacts.jsx
│   │   │   ├── editContacts.jsx
│   │   │   ├── index.jsx
│   │   │   ├── list.jsx
│   │   │   └── (other contact-related files)
│   │   └── (other scenes)
```

#### ❓ Why to not do this
- **PROS:** Reduced code duplication, faster development and temporarily happy dev 😊.
- **CONS:** Can lead to complex maintenance due to different behaviors and excessive conditional logic, making the temporary happiness fade 😅.

#### ✅ How to Do it
1. **Key Principles for Domain-Driven Design**
   1. **Understand the Business**: Know how the business operates and what users need.
   2. **Separate Business Objects**: Each business object should have its own logic. **🚫 Avoid mixing them**. Keep them separate in your design and code.
   3. **Create a Common Language**: Align the technical and business sides with clear terms and concepts. This helps in planning and defining project goals.

2. **Improved, domain-centric architecture**
```
app
├── src
│   ├── scenes
│   │   ├── clients
│   │   │   ├── createClients.jsx
│   │   │   ├── editClients.jsx
│   │   │   ├── index.jsx
│   │   │   ├── list.jsx
│   │   │   └── (other client-related files)
│   │   └── suppliers
│   │       ├── createClients.jsx
│   │       ├── editClients.jsx
│   │       ├── index.jsx
│   │       ├── list.jsx
│   │       └── (other supplier-related files)
```

Yes, it’s boring to duplicate code, but that prevents a future organizational mess with duplicate business logic everywhere, and nested rendering logic in all components.

[#Reference](https://petesena.medium.com/why-the-way-you-think-about-business-development-is-all-wrong-1d74c58c8628)

### 6.5 Monorepo Approach
#### ✖️ How to not do it
Consider the repositories for SELEGO. We have around 200 repositories. If every part of every project had its own repo—such as jobmaker-api, jobmaker-app, and more—you'd be managing at least 600 repositories. Onboarding new team members would require cloning multiple repos, like jobmaker-api and jobmaker-app, and setting up each one separately. Keeping track of everything would be overwhelming and complicated.

#### ✅ How to do it
**Disclaimer**: At Selego, our monorepo approach involves a single repository for all project components, such as the API and application, without a shared package.json. Each component operates with its own dependencies, ensuring flexibility and tailored project management.

Use a single monorepo to manage all project components, even with separate dependencies. This simplifies code management, bug fixes (with one pull request), and team collaboration, avoiding the complexity of multiple repositories. This approach enhances project management and efficiency, especially for small teams.
```
selego-monorepo/
│
├── app/
│   ├── src/
│   ├── public/
│   ├── package.json
│   ├── README.md
│
├── admin/
│   ├── src/
│   ├── public/
│   ├── package.json
│   ├── README.md
│
├── api/
│   ├── src/
│   ├── package.json
│   ├── README.md
│
└── README.md
```
#### ❓ Why to do this
**Centralized Code Management**: All code is in one repository, making it easier to oversee and manage.
**Streamlined Collaboration**: A single repository helps the team collaborate more efficiently by working within the same codebase.
**Consistent Tooling**: Using the same set of tools across all projects ensures consistency and reduces the learning curve for new team members.
**Simplified Dependencies**: Managing dependencies is straightforward since all parts of the project are in one place.  
**Enhanced Code Reuse**: Easier sharing and reuse of code across projects speed up development and reduce duplication.

### 6.6 Best Practices for Starting a Project
#### ✅ How to do it
When starting a new project, prioritize speed and simplicity:
Hardcode credentials like email and password directly in the login screen to enable quick access for anyone. It’s acceptable to use .env files and push them to GitHub at this early stage, as security is not a primary concern yet

```js
const match = config.ENVIRONMENT === "development" || (await user.comparePassword(password));
if (!match) return res.status(401).send({ ok: false, code: EMAIL_OR_PASSWORD_INVALID });
```

### 6.7 Service Code Approaches
Services are another important part for our projects. Every project has a folder that contains services that are crucial for code reuse across projects. They handle interactions with external APIs, like Brevo or Webflow, and should be designed to be modular and reusable. However, developers sometimes mix business logic into service code, which can lead to confusion.

Here's an example of a "Webflow" service:
#### ✖️ How to Not Do It
In this approach, the service code includes specific business logic and nested functions, making it less modular and harder to reuse across different projects:

```js
const { WEBFLOW_TOKEN, ENVIRONMENT } = require("../config");

const sendRequest = async (path, method, data) => {
  try {
    const url = `https://api.webflow.com/v2${path}`;

    const headers = {
      Authorization: `Bearer ${WEBFLOW_TOKEN}`,
      "accept-version": "1.0.0",
    };

    if (method === "post" || method === "put" || method === "patch") {
      headers["Content-Type"] = "application/json";
    }

    const response = await axios({ method, url, headers, data, validateStatus: () => true });

    if (response.data && response.data.message) return { ok: false, errorData: response.data };

    return { ok: true, data: response.data };
  } catch (error) {
    console.error("Error:", error);
    return null;
  }
};

async function get(path, params = null) {
  let fullPath = path;

  if (params) {
    const cleanedParams = Object.fromEntries(Object.entries(params).filter(([_, value]) => value !== undefined));
    fullPath += "?" + new URLSearchParams(cleanedParams);
  }

  return await sendRequest(fullPath, "get");
}

async function post(path, body = null) {
  return await sendRequest(path, "post", body);
}

async function put(path, body = null) {
  return await sendRequest(path, "put", body);
}

async function patch(path, body = null) {
  return await sendRequest(path, "patch", body);
}

async function remove(path) {
  return await sendRequest(path, "delete");
}

async function listSites() {
  try {
    const response = await get(`/sites`);
    return response;
  } catch (error) {
    console.error("Error fetching list of sites:", error);
    return { ok: false, errorData: error };
  }
}

async function getSite(id) {
  try {
    const response = await get(`/sites/${id}`);
    return response;
  } catch (error) {
    console.error("Error fetching site:", error);
    return { ok: false, errorData: error };
  }
}
```

#### ✅ How to Do It
In this approach, the service code is more modular and reusable, focusing only on core service functionality:

```js
const fetch = require('node-fetch');
const { URLSearchParams } = require("url");
const { WEBFLOW_TOKEN } = require("../config");

// https://developers.webflow.com/data/reference/rest-introduction

class Api {
  constructor() {
    this.token = WEBFLOW_TOKEN;
  }

  async sendRequest(path, method, body) {
    try {
      await new Promise((resolve) => setTimeout(resolve, 1000));

      const url = `https://api.webflow.com/v2${path}`;
      const headers = {
        Authorization: `Bearer ${this.token}`,
        "accept-version": "1.0.0",
        "Content-Type": method === "post" || method === "put" || method === "patch" ? "application/json" : undefined
      };

      const options = { method, headers, body: body ? JSON.stringify(body) : null };
      const response = await fetch(url, options);
      const result = await response.json();

      return result;
    } catch (error) {
      console.error("Error:", error);
      return null;
    }
  }

  async get(path, params = null) {
    let fullPath = path;
    if (params) fullPath += "?" + new URLSearchParams(params);
    return await this.sendRequest(fullPath, "get");
  }

  async post(path, body = null) {
    return await this.sendRequest(path, "post", body);
  }

  async put(path, body = null) {
    return await this.sendRequest(path, "put", body);
  }

  async patch(path, body = null) {
    return await this.sendRequest(path, "patch", body);
  }

  async remove(path) {
    return await this.sendRequest(path, "delete");
  }
}

const api = new Api();
```
#### ❓ Why to Do This
- **Modularity**: The second approach encapsulates service-related functionality within a single Api class, making the code more modular and easier to maintain.
- **Reusability**: By focusing on core service methods and avoiding project-specific logic, the service code is more reusable across different projects.
- **Clarity**: The code is cleaner and more focused, with business logic separated from service functions, enhancing readability and maintainability.
- **Simplified Management**: Managing and extending the service is simpler because changes are confined to a single class, avoiding scattered functions and business-specific code.

### 6.8 Create Small PRs

In our project, we follow the **GitHub Flow** model, which means the `main` branch should always be in a deployable state. It’s our source of truth, and everything we do revolves around ensuring it remains clean, stable, and ready for production.

#### Why Small PRs Matter:

1. **Faster Reviews**: Small PRs are quicker to review, making it easier for your team to understand the changes.
2. **Easier Deployments**: With smaller changes, deployments are smoother and faster, reducing the risk of introducing bugs.
3. **Frequent Releases**: By keeping your PRs small, you can release updates more frequently, which keeps your project moving forward.

#### ✖️ How Not to Do It:
Imagine you’re working on two different features: user authentication and task application. ⚠️ You might think it’s fast and efficient to bundle both into one PR, thinking it's the logical next step and that you’re killing two birds with one stone. ⛔ Wrong! This will only lead to confusion and delays.

PR with mixed features: 
```javascript
// User authentication function
const authenticateUser = async (username, password) => {
  try {
    const { ok, data } = await api.post('/login', { username, password });
    if (!ok) return;
    setUser(data);
  } catch (error) {
    console.error('Authentication failed:', error);
  }
};

// User applying to a task
const applyToTask = async (taskId, userId) => {
  try {
    const { ok } = await api.post(`/tasks/${taskId}/apply`, { userId });
    if (!ok) return;
    fetchTasks(); // Update State/UI
  } catch (error) {
    console.error('Task application failed:', error);
  }
};
```

#### ❓ Why to not do this
- The PR becomes confusing and hard to review since it’s dealing with unrelated areas of the codebase.
- If one part of the PR is problematic, it could delay the release of the other feature and this will slow everything down (We lose money 💸).
- Testing becomes more complex, increasing the risk of bugs.

#### ✅ How to Do It:
Now, let’s split these into two separate PRs.

**PR 1: Authentication System**
```javascript
const authenticateUser = async (username, password) => {
  try {
    const { ok, data } = await api.post('/login', { username, password });
    if (!ok) return;
    setUser(data);
  } catch (error) {
    console.error('Authentication failed:', error);
  }
};
```

**PR for User Applying to a Task:**
```javascript
const applyToTask = async (taskId, userId) => {
  try {
    const { ok } = await api.post(`/tasks/${taskId}/apply`, { userId });
    if (!ok) return;
    fetchTasks(); // Update State/UI
  } catch (error) {
    console.error('Task application failed:', error);
  }
};
```

#### ❓ Why to do this
- Each PR is focused on a single feature, making it easier to review and understand.
- You can deploy the authentication system without waiting for unrelated features to be finalized.
- If any issues arise, it’s easier to pinpoint the problem.

#### Conclusion:
Keep your PRs small, focused, and relevant to a single feature or subject. This practice not only improves the quality of the code but also streamlines the development process, making your team more efficient and your project more reliable.
