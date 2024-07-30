# Selego Style Guide

## Introduction
This guide provides standards and best practices for developing projects at Selego, ensuring consistency, readability, and maintainability of our code.

### Purpose
To establish a consistent coding style and workflow for all projects, making collaboration easier and reducing technical debt.

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
   - [1.1. Code Readiness](#11-code-readiness)
2. [Back-end](#2-back-end)
   - [2.1. The Post Search](#21-the-post-search)
3. [Front-end](#3-front-end)
4. [DevOps](#4-devops)
5. [NoCode](#5-nocode)
6. [Project](#6-project)
   - [6.1. Architecture](#61-architecture)

## 1. Javascript

### 1.1. Code Readiness

Implementing early returns in your code can significantly enhance readability and maintainability. Instead of nesting logic within conditional blocks, an early return can simplify the structure of your functions by handling edge cases upfront and allowing the core logic to be more linear and easier to follow. Consider the following example:

```js
const { data, ok } = await api.get(`/meeting/${id}`);
if (ok) {
  console.log("Wow, now I have data, I'm going to set it.");
  setMeeting(data);
  ...
}
```
This code can be made more readable by applying an early return:

```js

const { data, ok } = await api.get(`/meeting/${id}`);
if (!ok) return;

console.log("Wow, now I have data, I'm going to set it.");
setMeeting(data);

...
```
By immediately returning when the condition is not met, the main logic is not indented and the function becomes more straightforward to understand.



## 2. Back-end

### 2.1. The Post Search

Here is the method we use to fetch lists. In most cases, we need to perform extensive filtering on these lists, and a classical GET request can become really messy due to the querying. Instead, we use a POST request for more flexible querying.

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


By using a POST request, we can send a complex object in the body of the request, which allows us to construct more flexible and powerful queries.



### Respect 1 Post Route, 1 Object Created

In RESTful API design, adhering to the principle of "1 POST route, 1 object created" is crucial for maintaining clear separation of concerns and simplifying the API's behavior. This approach ensures that each endpoint has a single responsibility, making the API more predictable and easier to maintain.

For example, consider a scenario where a single POST request is used to create multiple objects such as an announcement, a company, and more. This can quickly lead to a bloated and complex endpoint, as seen in the following code snippet from a GitHub repository:

[View the code](https://github.com/selego/linkera/blob/main/api/src/controllers/annonce.js)

In this code, the POST request is responsible for creating multiple objects within a single route. This approach can lead to several issues:
- **Increased Complexity:** Handling multiple objects in a single route increases the complexity of the controller, making it harder to read, test, and maintain.
- **Tightly Coupled Logic:** The creation logic for different objects becomes tightly coupled, which can make future changes or debugging more difficult.
- **Separation of Concerns:** By combining multiple responsibilities into a single route, the separation of concerns principle is violated, leading to a less modular and more error-prone codebase.

Adhering to the principle of "1 POST route, 1 object created" helps mitigate these issues by ensuring each route is responsible for a single task. This results in:
- **Simpler and Cleaner Code:** Each controller method remains focused on a single responsibility, making the code easier to read and maintain.
- **Improved Debugging:** With each route handling only one type of object, debugging becomes more straightforward.
- **Better Modularity:** Decoupled logic allows for easier modifications and enhancements in the future without affecting other parts of the code.

In conclusion, respecting the principle of "1 POST route, 1 object created" is essential for maintaining a clean, modular, and maintainable codebase. It helps in keeping the API design straightforward and the controller logic simple, ultimately leading to better software development practices.


```js


```



```js

```

```js

```

## 3. Front-end

```js

```
## 6. Project

### 6.1. Architecture

We try to keep the same project architecture because:
- It is much easier to jump from one project to another within the company.
- We try to keep code look like it's written from the same person, even througout projects, same for the way we structure our files.

```plaintext
project-root
│
├── api
│   └── (api-related files and configurations)
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


### Usage of Joi in Early Phases

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


