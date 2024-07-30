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


