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
$ npm i express
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
## Configure the Hello World datasource

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
1. Enter `hello-world` and click `Next`
1. For document id click on `Auto-Id`
1. Create a field titled `firstName` as `string` and give it a value
1. Create a field titled `lastName` as `string` and give it a value
1. Click on `Save`

You have just created your hello world collection.

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

1. Under that comment block, start forming your `.env` constants. Prepend each setting with `FIREBASE_` and uppercase the setting name , like so:
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

Now you have a `.env` file with the firebase config settings. These need to be kept secret, so let's make sure we don't check in them into the repo. Add `app/.env` in your `.gitignore`

1. In your editor, create a new file in `./api` titled `datasource.js`
1. Add the following:
```js

const { initializeApp, cert } = require('firebase-admin/app')
const { getFirestore } = require('firebase-admin/firestore')

const serviceAccount = {
  type: process.env.FIREBASE_TYPE,
  project_id: process.env.FIREBASE_PROJECT_ID,
  private_key_id: process.env.FIREBASE_PRIVATE_KEY_ID,
  private_key: process.env.FIREBASE_PRIVATE_KEY,
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

export const db = getFirestore()
```


<!-- =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=- -->
&nbsp;  
## Develop & deploy the Hello World API

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
const app = express()
const port = 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```
3. In your `package.json` add
```json
  "scripts": {
    "start": "node server.js",
    "start:dev": "nodemon server.js"
  },
```

4. Test your API server
```bash
$ cd ./api
$ npm run start:dev
```

5. In your browser, go to `http://localhost:3000/` and your API should respond with a `Hello World`  

1. Install the firebase admin to your api 
```bash
$ npm i firebase-admin
```

7. Generate the secrets

1. turn them into constants in a .env
1. Add `api/.env` in your `./.gitignore` 
1. Create your API's datasource config file: `/api/datasource.js`
1. Add the following into your datasource config file
```js
const { initializeApp } = require('firebase-admin/app');

const serviceAccount = {
... do this
}

initializeApp({
  credential: cert(serviceAccount)
});

export const db = getFirestore();
```

10. Call the `dotenv` config ONLY IN PROD.
```js
// read .env only in dev
if (process?.env?.NODE_ENV !== "production") {
  require("dotenv").config();
}
```   

&nbsp;  
## Develop & deploy the Hello World App

We will create a page that executes a GET request to our `/hello-world` endpoint and displays:
```
Hello World. My name is {firstName} {lastName}
```

<!--


$ npm i express body-parser firebase cors jsonwebtoken

1. Click on Authentication and `Get Started`
1. Click on Email/Password
1. Enable & save

You have just created your authentication source and your hello world collection.


-->