[How to build a FERN web app](./index.md) :
# Skeleton Application Setup


<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Setup datasources

1. Go to `mockaroo.com` and fill the form:
```
userProfileId, GUID
firstName, First Name
lastName, Last Name
phoneNumber, Phone
rows: 12
format: JSON
```

1. Click on `Download Data` and open the json file in your VS code
1. Copy one user to be your first user

1. Go to Firebase and click on the navigation `Build` then `Authentication`
1. Click on `Get started` and select `Email/Password` as a `Native provider`  
1. Turn on `Enable`
1. Click on `Add user` and add a temporary email and password and submit.

1. Click on the navigation `Build` then `Firestore Database`
1. Click on `Start collection`
1. Enter `UserProfile` as a collection id and click on `Next`
1. On the document id click on `Auro-ID`
1. Create a new field: { Field: firstName, Type: string, Value: Phil }
1. Create a new field: { Field: lastName, Type: string, Value: Skaroulis }
1. Create a new field: { Field: phoneNumber, Type: string, Value: 555-555-5555 }
1. Click on `Save`


<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Create Route structure

1. Create a folder titled `sub-apps` at the root of `./api`
1. Create a file titled `routes.js` in `./api/sub-apps`

```js
const express = require("express");

const app = express();

// import custom routes
const modules = {
  sessions: require("../modules/sessions/routes"),
  userProfiles: require("../modules/user-profiles/routes"),
};

// map routes to modules
app.use("/sessions", modules.sessions);
app.use("/user-profiles", modules.userProfiles);

module.exports = app;
```

3. Towards the top, in the require section of your `./api/index.js`, add
```js
// import the sub-apps
const routes = require("./sub-apps/routes");
```

4. In the same file, create a section for your `middleware`
```js
// middleware
app.use(cors())
app.use(routes);
```



<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Create API Module structure

1. Create a folder titled `modules` at the root of `./api`
1. Create a folder titled `sessions` in `./api/modules`
1. Create a file titled `data-access-layer.js` in `./api/modules/sessions`
1. Create a file titled `routes.js` in `./api/modules/sessions`
1. Create a folder titled `user-profiles` in `./api/modules`
1. Create a file titled `data-access-layer.js` in `./api/modules/user-profiles`
1. Create a file titled `routes.js` in `./api/modules/user-profiles`




<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Create API Data Access Layer











In this phase, we will develop & deploy the following modules:
- Signup
- Login
- Home
- Profile
- Logout

Process for each module: 
- document the module in the README
- write failing tests
- write internal docs (comments)
- write logic
- write passing tests
- update README

Each module requires the following:
1. Datasources (authentication & user)
1. API Data Access Layer
1. API routes (endpoints)
1. App Data Access Layer
1. App Browser Routes
1. App UI Components 
1. Input data validation
1. Cross-tier error handling
