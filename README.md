SELEGO

Whitepaper/dev training/CTO ready

![Original Document](https://docs.google.com/document/d/1pPG7QrUunatppNWOKe2lNAJsis1wlB6tL11T5BzjkXo/edit)

# Table of contents

<!-- - [1. Javascript](#1.-Javascript)
- [2. Back-end](#2.-Back-end)
- [3. Front-end](#3.-Front-end)
- [4. DevOps](#4.-DevOps)
- [5. NoCode](#5.-NoCode)
- [6. Project](#6.-Project) -->

- [Table of contents](#table-of-contents)
  - [1. Javascript](#1-javascript)
  - [2. Back-end](#2-back-end)
    - [2.1. Add a file controller](#21-add-a-file-controller)
  - [3. Front-end](#3-front-end)

## 1. Javascript

```js
// handling errors
// bad
const fetchData = async () => {
  try {
    const { data } = await api.get("/whateveryouwanttoget");
    setData(data);
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};

// good
// clean! now we don't care if something happens. we're handling errors gracefully.
const fetchData = async () => {
  const { data, ok } = await api.get("/whateveryouwanttoget");
  if (ok) {
    setData(data);
  } else {
    setError(new Error("Failed to fetch data"));
  }
};

// Change Amplification
// BAD EXAMPLE BACKEND
app.get('/api/users/:userId', async (req, res) => {
    try {
        const users = await User.find({ _id: req.params.userId }); // returns an array
        res.json({ ok: true, data: users });
    } catch (error) {
        res.json({ ok: false, error: error.message });
    }
});

// BAD EXAMPLE FRONTEND
<div>
    {user ? (
        <>
            <h1>{user[0].name}</h1> {/* Accessing array on each line */}
            <p>{user[0].email}</p>
        </>
    ) : (
        <p>Loading...</p>
    )}
</div>

// GOOD EXAMPLE BACKEND
app.get('/api/users/:userId', async (req, res) => {
    try {
        const user = await User.findOne({ _id: req.params.userId }); // returns an object
        res.json({ ok: true, data: user });
    } catch (error) {
        res.json({ ok: false, error: error.message });
    }
});

// GOOD EXAMPLE FRONTEND
.then(({ data }) => setUser(data)) // setting object to state
...
<h1>{user.name}</h1> {/* Accessing object directly */}
<p>{user.email}</p>

// Don’t play with only part of the object
// bad
const getInvitations = async () => {
    const { data, ok } = await api.post('/membership_event/search', {
        event_id: id
    });
    if (!ok) {
        toast.error(t('error_loading_invitations'))
        return
    }
    setInvitations(data.map(membership => membership.user_id))
}

// good
const getInvitations = async () => {
    // ...
    setInvitations(data)
}

// early return
// bad
const { ok } = await api.put(`/meeting/url/${id}`);
if (ok) {
    const cpy = meetings;
    const index = cpy.findIndex((e) => e._id === id);
    // ...
}

// good
const { ok } = await api.put(`/meeting/url/${id}`);
if (!ok) return
const cpy = meetings;
const index = cpy.findIndex((e) => e._id === id);

// filtering
// bad
const response = await api.get("/meeting");
setMeetings(response.data.filter((e) => !e.isDeleted));

// good
const response = await api.post("/meeting/search",{deleted:false });
setMeetings(response.data);

// Update after a delete
// bad
async function handleDelete(id) {
    const confirm = window.confirm("Are you sure ?");
    if (!confirm) return;
    await api.remove(`/product/${id}`);
    toast.success("Successfully removed");
    setProducts(products.filter((product) => product.id !== id));
}

// good
// Delete product
async function handleDelete(id) {
    const confirm = window.confirm("Are you sure ?");
    if (!confirm) return;
    await api.remove(`/product/${id}`);
    toast.success("Successfully removed");
    fetch()
}

// Same component for two routes / Switching between two business objects in the same component
// bad
// router
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

// list.js
const List = () => {
const [contacts, setContacts] = useState();
const [selectedContact, setSelectedContact] = useState();
const isClient = location.pathname.indexOf("/clients") !== -1;
const type = isClient ? "client" : "supplier";


// good
// -> duplicate component !


```

## 2. Back-end

### 2.1. Add a file controller

bad<br>
handle file upload in every controller

```js
if (
  req.body.hasOwnProperty("thumbnail") &&
  req.body.thumbnail.hasOwnProperty("rawBody")
) {
  const base64ContentArray = req.body.thumbnail.rawBody.split(",");
  const contentType = base64ContentArray[0].match(
    /[^:\s*]\w+\/[\w-+\d.]+(?=[;| ])/
  )[0];
  const extension = req.body.thumbnail.name.split(".").pop();
  const buffer = new Buffer(base64ContentArray[1], "base64");
  var url = await uploadVideoToS3FromBuffer(
    `thumbnails/${video._id}.${extension}`,
    buffer,
    contentType
  );
  if (req.body.cropped)
    await uploadVideoToS3FromBuffer(
      `cropped/${video._id}.${extension}`,
      buffer,
      contentType
    );
  video.thumbnail = url.replace(/^https?:\/\//i, "");
  await video.save();
}
```

good <br>
create a dedicated controller

```js
const express = require("express");
const router = express.Router();
const crypto = require("crypto");
const { uploadToS3FromBuffer } = require("../utils");

router.post("/", async (req, res) => {
   const { files, folder } = req.body;

   if (!folder) return res.status(400).send({ ok: false, message: "No folder specified" });

   if (!files) return res.status(400).send({ ok: false, message: "No files uploaded" });

   // Ensure 'files' is always an array
   const filesArray = Array.isArray(files) ? files : [files];

   const uploadPromises = filesArray.map((file) => {
       co
nst base64ContentArray = file.rawBody.split(",");
       const contentType = base64ContentArray[0].match(/[^:\s*]\w+\/[\w-+\d.]+(?=[;| ])/)[0];
       const extension = file.name.split(".").pop();
       const buffer = Buffer.from(base64ContentArray[1], "base64");
       const uuid = crypto.randomBytes(16).toString("hex");
       return uploadToS3FromBuffer(`file${folder}/${uuid}/${file.name}.${extension}`, buffer, contentType);
   }).filter(promise => promise !== null); // Filter out the nulls

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

conventions

```js
// always return data object
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

```js
// our post search

// bad
// controller : user
router.get("/:id/devices", async (req, res) => {
  try {
    const data = await DeviceModel.find({ userId: req.params.id });
    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});

// good
// controller : device
router.post("/search", async (req, res) => {
  try {
    const query = {};
    if (req.body.hasOwnProperty(req.body.userId))
      query.userId = req.body.userId;
    const data = await DeviceModel.find(query);
    return res.status(200).send({ ok: true, data });
  } catch (error) {
    capture(error);
    res.status(500).send({ ok: false, code: SERVER_ERROR, error });
  }
});

// bad
const language = window.localStorage.getItem("i18nextLng");
await api.post("/api/users/language", { language });

// good
const language = window.localStorage.getItem("i18nextLng");
await api.put("/api/users/${id}", { language });

// security injections
// bad
router.put(
  "/:id",
  passport.authenticate(["admin", "user"], { session: false }),
  async (req, res) => {
    try {
      const data = await Company.findOneAndUpdate(
        { _id: req.params.id },
        req.body
      );
      return res.status(200).send({ ok: true, data });
    } catch (error) {
      capture(error);
      res.status(500).send({ ok: false, code: SERVER_ERROR, error });
    }
  }
);

// good
router.get(
  "/:id",
  passport.authenticate(["user"], { session: false }),
  async (req, res) => {
    try {
      const query = {};

      query.organisationId = req.user.organisationId;
      query._id = req.params.id;

      const data = await MissionObject.findOne(query);
      return res.status(200).send({ ok: true, data });
    } catch (error) {
      capture(error);
      res.status(500).send({ ok: false, code: SERVER_ERROR, error });
    }
  }
);
```

## 3. Front-end

```js
// Don't do a render function for 2 lines of code.
// bad
function render() {
    if (tab === "debate") return <Debate debate={debate} setDebate={setDebate} />;
    if (tab === "edit") return <Edit onSave={setDebate} />;
}

<div>{render()}</div>

// good
function render() {
    if (tab === "debate") return <Debate debate={debate} setDebate={setDebate} />;
    if (tab === "edit") return <Edit onSave={setDebate} />;
}

<div>{render()}</div>

// useless loading component
// bad
<LoadingComponent className="" ...moreUselessPropsAboutColors disabled={isLoading} />

// good
isLoading && <div>Loading....</div>

// Async functions inside useEffect
// bad
useEffect(() => {
    ; (async () => {
      const { data } = await api.get(`/company/${match.params.id}`)
      setCompany(data)
      setValues(data)
    })()
}, [match.params.id])

// good
useEffect(() => {
    fetchWhatever();
}, [match.params.id])


async fetchWhatever = () => {
const { data } = await api.get(`/company/${match.params.id}`)
    setCompany(data)
    setValues(data)
}

// don't abstract all the CRUD operations

// bad

const getActions = () => API.get({ path: '/action' })

// do something in the flow
// while in the flow...
const actions = await getActions()
setActions(actions.data)
// ... keep the flow going on

// good

// do something in the flow
// while in the flow...
const response = await API.get({ path: '/action' })
if (!response.ok) response alert(response.error);
setActions(response.data)
// ... keep the flow going on
```
