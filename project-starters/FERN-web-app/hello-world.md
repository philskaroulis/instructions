[How to build a FERN web app](./index.md) :
# Hello World


<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Setup folder structure

```bash
# === setup root project and repo ===
$ cd ~/Development/Projects
$ md <project-name>
$ cd <project-name>
$ git init -b main

# === setup the app ===
$ npx create-react-app app
$ rm -rf app/.git
$ mv ./app/.gitignore .
$ code .
```

In your editor, open `.gitignore`. Give your git ignore some structure by organizing it into three sections: root, api and app.
```bash
# root

# api
# prepend all files and folders with "api/"
api/node_modules

# app
# prepend all files and folders with "app/"
app/node_modules

```


<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Setup the API server
```bash
$ md api
$ cd api
$ npm init -y
$ npm i express cors
$ npm i -D nodemon dotenv
```


<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Setup Git
```bash
$ cd ..
$ git add .
$ git commit -m "initial commit"
$ gh repo create

# Note: When prompted, select "Push an existing local repository to GitHub" 
# and enter the desired name for your repository.
```

At this point you are ready to code, commit and push to your repo's `origin`.


<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Create the datasource

We will create a collection that holds a document with the object:
```json
{
    "firstName": ,
    "lastName": ,
}
```

Follow these steps:
1. Go to Firebase and create a new project.  

1. Turn off Google Analytics
1. On left navigation manu, click on `Build`
1. Click on `Firestore Database` and `Create database`
1. For rules, click on `Start in test mode`
1. Select your closest location (like `us-east4`)
1. Click on `Start collection`
1. Enter `MyName` and click `Next`
1. For document id click on `Auto-Id`
1. Create a field titled `firstName` as `string` and give it a value
1. Create a field titled `lastName` as `string` and give it a value
1. Click on `Save`

You have just created your hello world collection.


<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Configure the datasource

Now let's capture some config settings for our project.

1. Go to project settings

1. Go to service accounts
1. Click on `Generate new private key`
1. Open the downloaded firebase config json file in your editor
1. In your editor, create a new file in `./api` titled `.env`
1. Copy the content of the config json
1. In `.env`, create a comment block and paste your config settings from the firebase, like so:
```js
/*
{
  "type": "...",
  "project_id": "...",
  "private_key_id": "...",
  "private_key": "...",
  "client_email": "...",
  "client_id": "...",
  "auth_uri": "...",
  "token_uri": "...",
  "auth_provider_x509_cert_url": "...",
  "client_x509_cert_url": "..."
}
*/
```

8. Under that comment block, start forming your `.env` constants. Prepend each setting with `FIREBASE_` and uppercase the setting name , like so:
```bash
FIREBASE_TYPE="..."
FIREBASE_PROJECT_ID="..."
FIREBASE_PRIVATE_KEY_ID="..."
FIREBASE_PRIVATE_KEY="..."
FIREBASE_CLIENT_EMAIL="..."
FIREBASE_CLIENT_ID="..."
FIREBASE_AUTH_URI="..."
FIREBASE_TOKEN_URI="..."
FIREBASE_AUTH_PROVIDER_X509_CERT_URL="..."
FIREBASE_CLIENT_X509_CERT_URL="..."
```

Now you have a `.env` file with the firebase config settings. 

**NOTE:** These need to be kept secret, so let's make sure we don't check in them into the repo. Add `app/.env` in your `.gitignore`

Now let's install the firebase admin to your api 
```bash
$ npm i firebase-admin
```

In your editor, create a new file in `./api` titled `datasource.js`.
Add the following:
```js
const { initializeApp, cert } = require('firebase-admin/app')
const { getFirestore } = require('firebase-admin/firestore')

const serviceAccount = {
  type: process.env.FIREBASE_TYPE,
  project_id: process.env.FIREBASE_PROJECT_ID,
  private_key_id: process.env.FIREBASE_PRIVATE_KEY_ID,
  private_key: process.env.FIREBASE_PRIVATE_KEY.replace(/\\n/g, '\n'),
  client_email: process.env.FIREBASE_CLIENT_EMAIL,
  client_id: process.env.FIREBASE_CLIENT_ID,
  auth_uri: process.env.FIREBASE_AUTH_URI,
  token_uri: process.env.FIREBASE_TOKEN_URI,
  auth_provider_x509_cert_url: process.env.FIREBASE_AUTH_PROVIDER_X509_CERT_URL,
  client_x509_cert_url: process.env.FIREBASE_CLIENT_X509_CERT_URL
}

initializeApp({
  credential: cert(serviceAccount),
})

const db = getFirestore()

module.exports = db
```
Note: for the `private_key` you have to replace `\\n` with `\n`.

Let's make sure that, when the express server starts, it reads the `.env` file and copies the settings into `process.env`, like so:
```js
// read .env only in dev
if (process?.env?.NODE_ENV !== "production") {
  require("dotenv").config({ path: __dirname + '/.env' })
}
```   
**NOTE:** Execute the `dotenv.config()` ONLY IN DEV. In production, those settings will be declared on the platform and automatically copied into `process.env`.


<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Add a Hello World endpoint

We will create a `/hello-world` endpoint that queries the collection and returns the object
```json
{
    "firstName": ,
    "lastName": ,
}
```

Follow these steps:
1. Create your API's main file: `/api/index.js`  

2. Copy the [Express Hello world example](https://expressjs.com/en/starter/hello-world.html) into your `/api/index.js`
```js
const express = require('express')
const cors = require('cors')
const app = express()
const port = process.env.PORT || 5000

app.use(cors())

app.get('/', (req, res) => {
  res.status(200).send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```
3. In your `package.json` add
```json
  "scripts": {
    "start": "node index.js",
    "start:dev": "nodemon index.js"
  },
```

4. Test your API server
```bash
$ cd ./api
$ npm run start:dev
```

5. In your browser, go to `http://localhost:5000/` and your API should respond with a `Hello World`  

Now let's make the call to the datasource:
```js

app.get('/hello-world', (req, res) => {
  const { id } = req.query

  if (!id) {
    const msg = "Bad Request: Missing id."
    console.error(msg)
    res.status(400).json({error: msg})
    return
  }

  const db = require("./datasource")

  const docRef = db.collection("MyName").doc(id)

  docRef.get()
    .then((doc) => {
      if (doc.exists) {
        const { firstName, lastName } = doc.data()
        res.status(200).send(`Hello. My name is ${firstName} ${lastName}.`)
      } else {
        console.error("id not found")
        res.status(401).json(result)
      }
    })
    .catch((error) => {
      console.error(error)
      res.status(401).json(result)
    })

})
```
In your Firestore, copy the document id that holds your name.

In your browser, go to `http://localhost:5000?id=PASTE-DOC-ID` and your API should respond with a `Hello. My name is...`  

Last step: respond with a json object instead of a string:
```js
        res.status(200).send({ firstName, lastName })
```

<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Call Hello World endpoint from the front-end

```js
import { useEffect, useState } from "react"
...
  const [firstName, setFirstName] = useState();
  const [lastName, setLastName] = useState();

  // get data
  useEffect(
    () => {
      try {
        const endpoint = process.env.REACT_APP_API_ENDPOINT;
        fetch(endpoint + "hello-world?id=c7ICVeZJFf9SfYvT85Ov")
          .then((response) => response.json())
          .then((data) => {
            setFirstName(data.firstName);
            setLastName(data.lastName);
          });
      } catch (error) {
        console.error(error);
      }
    },
    // eslint-disable-next-line
    []
  );
...
<h1>
  Hello. My name is {firstName} {lastName}.
</h1>
```

In your editor, create a new file in `./app` titled `.env.development` and add the following:
```bash
REACT_APP_API_ENDPOINT=http://localhost:5000/
```

In your editor, create a new file in `./app` titled `.env.test` and add the following:
```bash
REACT_APP_API_ENDPOINT=http://localhost:5000/
```
 

<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Deploy the Hello World API

Let's start with deploying the Express API to `Heroku`.

In your editor, create a file named `Procfile` in the `api` folder and enter the following:
```json
web: npm start
```

Log into your `Heroku` account and enter your `Personal` dashboard.

Click on the `New` button and select `Create new app`.

Enter the app name (like `username-appname`)and click on `Create app`.

When taken to the app, click on the `Settings` sub-menu and the click on `Reveal Config Vars`.

One by one add the key/values from your settings in your `.env` file.
```bash
FIREBASE_TYPE="..."
FIREBASE_PROJECT_ID="..."
FIREBASE_PRIVATE_KEY_ID="..."
FIREBASE_PRIVATE_KEY="..."
FIREBASE_CLIENT_EMAIL="..."
FIREBASE_CLIENT_ID="..."
FIREBASE_AUTH_URI="..."
FIREBASE_TOKEN_URI="..."
FIREBASE_AUTH_PROVIDER_X509_CERT_URL="..."
FIREBASE_CLIENT_X509_CERT_URL="..."
```

If you haven't already, install the `Heroku CLI`:
```bash
$ npm i -g heroku
```

Next, let's add a helper script to the `scripts` section of your `package.json`:
```json
  "scripts": {
    "deploy": "cd ..; git subtree push --prefix api heroku main",
  }
```
Note: The reason to use a `subtree push --prefix` is that a nodejs app on Heroku requires a `package.json` at the root of the directory structure. In our case, the json file is in the api folder.

Also this:
```json
  "engines": {
    "node": "18.x"
  }
```

Log into your `Heroku CLI`:
```bash
$ heroku login
```

Add `Heroku` as a remote to your project:
```bash
$ cd api
$ heroku git:remote -a username-appname
```

Now you can commit and deploy:
```bash
$ cd ..  # go to projet root
$ git add .
$ git commit -m "added deployment configuration"
$ cd api
$ npm run deploy 
```

<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Deploy the Hello World App

Now let's deploy the React app to `Github Pages`.

In your Github repo got to `Settings` then `Pages`.

Make sure `Source` is set to `Deploy from a branch`.

Set `Branch` to your `main` and click `Save`.

Now add a `homepage` property to your `package.json`.
```json
  "homepage": "https://GITHUB-USERNAME.github.io/PROJECT-NAME",
```

Install the `Github Pages` client:
```bash
$ npm i gh-pages
```

Next, let's add helper scripts to the `scripts` section of your `package.json`:
```json
  "scripts": {
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build",
  }
```
Note: "predeploy" will run automatically before deploy is run.

In your editor, create a new file in `./app` titled `.env.production` and add the following:
```bash
REACT_APP_API_ENDPOINT=https://username-appname.herokuapp.com/
```

Let's deploy the app:
```bash
$ npm run deploy
```

Now go back and set `Branch` to your `gh-pages` and click `Save`.

**NOTE:** Give github 15-30 minutes to index your website.
