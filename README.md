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
   - 1.1 [Code Readiness](#11-code-readiness)
     - 1.1.1 [Early Returns](#111-early-returns)
     - 1.1.2 [Easy Confirmation](#112-easy-confirmation)
   - 1.2 [Beginner Mistakes we see way to often](#12-beginner-mistakes-we-see-way-to-often)
     - 1.2.1 [Filters in the frontend](#121-filters-in-the-frontend)
2. [Back-end](#2-back-end)
   - 2.1 [The Post Search](#21-the-post-search)
   - 2.2 [Respect 1 Post Route, 1 Object Created](#22-respect-1-post-route-1-object-created)
   - 2.3 [Consistency in Route Naming](#23-consistency-in-route-naming)
3. [Front-end](#3-front-end)
   - 3.1 [Conventions on Calling API](#31-handling-api-responses)
   - 3.2 [Separating Concerns](#32-separating-concerns)
   - 3.3 [Avoid Partial Data Extraction](#33-avoid-partial-data-extraction)
4. [DevOps](#4-devops)
5. [NoCode](#5-nocode)
6. [Project](#6-project)
   -  6.1 [Architecture](#61-architecture)
   - 6.2 [Validation?](#62-usage-of-joi-in-early-phases)
   - 6.3. [Uploading Files](#63-how-to-upload-files)
   - 6.4. [Domain Scoping](#64-domain-scoping)


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
  if (!window.confirm({Are you sure ?})) return;
  await submit();
}
```

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