# How to build a FERN web app

**FERN**: Firebase, Express, React, Node.js

## Project Phases
1. [Project Definition](#project-definition)
1. [Hello World Environment Setup](#hello-world-environment-setup)
1. [Skeleton Application Setup](#skeleton-application-setup)
1. [Application Development](#application-development)

&nbsp;  

---
## Project Definition

&nbsp;  
**Project Deliverables:**
1. A GitHub repo with all code
1. A project README (descriptions and diagrams)
1. Link to deployed App
1. Link to deployed API
1. Presentation Video

&nbsp;  
**Application Requirements:**  
What will the user be able to do with the app?

Here's a brief list of the features:  
1.  
2.  
3.  

&nbsp;  
**App Technology Choices:**
1. App language: JavaScript (ES6)
1. App framework: React
1. Deployment platform

&nbsp;  
**API Technology Choices:**
1. API language: JavaScript (ES6)
1. API framework: Express
1. Deployment platform

&nbsp;  
**Diagrams:**
1. Database architecture 
1. App architecture, showing UI components and authentication
1. Overall architecture, showing App components, App DAL, API routes, API DAL, and Database

&nbsp;  

---
## Hello World Environment Setup

&nbsp;  
**Setup local environment**

```
# === setup root project and repo ===
$ cd ~/Development/Projects
$ md <project-name>
$ cd <project-name>
$ git init -b main
$ echo "app/node_modules" >> .gitignore
$ echo "api/node_modules" >> .gitignore

# === setup the app ===
$ npm create-react-app app
$ rm -rf app/.git

# === setup the api ===
$ md api
$ cd api
$ npm init -y
$ npm i express body-parser firebase cors jsonwebtoken
$ npm i -D nodemon dotenv

# === setup origin ===
$ cd ..
$ git add .
$ git commit -m "initial commit"
$ gh repo create

# Note: When prompted, select "Push an existing local repository to GitHub" 
# and enter the desired name for your repository.
```

At this point you are ready to code, commit and push to your repo's `origin`.

&nbsp;  
**Configure the Hello World datasource**

We will create a collection that holds a document with the object:
```
{
    firstName: ,
    lastName: ,
}
```

&nbsp;  
**Develop & deploy the Hello World API**

We will create a `/hello-world` endpoint that queries the collection and returns the object
```
{
    firstName: ,
    lastName: ,
}
```


&nbsp;  
**Develop & deploy the Hello World App**

We will create a page that executes a GET request to our `/hello-world` endpoint and displays:
```
Hello World. My name is {firstName} {lastName}
```



&nbsp;  

---
## Skeleton Application Setup

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

&nbsp;  

---
## Application Development

In this phase, we will develop & deploy the application's modules:
- (module name)
- (module name)
- (module name)

&nbsp;  
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
