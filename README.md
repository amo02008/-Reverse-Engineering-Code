# -Reverse-Engineering-Code

As a web developer I want a walk-through of the codebase so that I can use it as a starting point for a new project.and providing a walkthrough of the code itself. This This authentication application utilizes `sequalize` and `passport.js`. Below will be a walkthrough of the functionality of the app itself and the responsibility of each file. 

## App Functionality

When running this App on your localhost- you are able to log in, and log out. 

* The root page `"/"` - the root page.

* The `/login` - log in here. 

* The `/members` page - you are taken to this page once you are signed up using the email & password you provided.

## The Walkthrough

### Application folders/files


### **Package.json**

This file is used to list out the dependecies needed in your project. This hold numorous metadata that corresponds to the project. This also makes it easier for developers to share their project with others.

`npm install` - running this command on your terminal to install the following Node packages that are essetial for the appliacation:

* `bcryptjs` - password hashing function
* `express` - web framework for Node
* `express-session` - session middleware for Express
* `passport` - Express-compatible authentication middleware for Node.js
* `passport-local` - Passport strategy for authenticating with a local username and password instead of something like authenticating with Google
* `sequelize` - ORM for Node.JS
* `mysql2` - MySQL driver used with Sequelize

### **Server.js file**

```shell
./reverse-engineering-code
└── server.js
```

When initiating the server, `node.server.js` in your terminal, you can then view the application in `localhost:8080` in your browser. This root file runs the Javascript code to be able to have the server run. This file does the heavy lifting for you, so you're able to focus on your project. You will also need a route.js file to be able to run your application.

### **Routes/html-routes.js**

```shell
routes/
├── api-routes.js
└── html-routes.js

0 directories, 2 files
```
This file contains Express which is essential to define routes for common URL requests from the users browser. Whenever there is a request for the `"/"` root, `/login`, or `/members` page from the website, these routes will check to see if the user has created an account or not. 

If said user has no account on file,they will be re-directed to `/signup.html` page to create a new account. If they happen to log in succesfully they will be sent to `/members` route. Otherwise fail to login they will be re-directed to the `/signup.html` file or `/login` file.

### **The `public/` folder**

```
public/
├── js
│   ├── login.js
│   ├── members.js
│   └── signup.js
├── login.html
├── members.html
├── signup.html
└── stylesheets
    └── style.css

2 directories, 7 files
```

The main role of the `/public` folder is what is essentially shown to the user. The face of website. As explained above.

### **The `config/` folder**

```
config/
├── config.json
├── middleware
│   └── isAuthenticated.js
└── passport.js

1 directory, 3 files
```
When starting up the server one must create `passport_demo` database in your local development folder. `'config.json` is what specifies the configuration used in the database. For this folder we are using a  MYSQL database. You can run you database locally through MYSQL Workbench. Be aware that you are adding `.env` variables and any sensitive data out to the public, as that can put in  a security risk.

In the `isAuthenticated.js` file, a single function is exported and its only purpose is to check if the user is logged in.

This function is only called on the `/members` route. If the user is logged in, the app will continue with the request to the current `/members` route and continue to an anonymous function which `res.sendFile` the `members.html`. If the user isn't logged in, the app will redirect them to the `"/"`, which is the login page as defined in `html-routes.js`.

In the above case, because we are using a 307 temporary redirect from the `/signup` to `/member` route, the email and password in body of the original signup request is reused, which allows the user to be automatically logged in.

```javascript
// This is middleware for restricting routes a user is not allowed to visit if not logged in
module.exports = function(req, res, next) {
  // If the user is logged in, continue with the request to the restricted route
  if (req.user) {
    return next();
  }

  // If the user isn't logged in, redirect them to the login page
  return res.redirect("/");
};

```

`passport.js` is configured by setting up config/passport.js. In this configuration, we tell Passport we want to passport.use(new LocalStrategy), a Local Strategy, by using require("passport-local").Strategy.

Passport uses what are termed strategies to authenticate requests. Strategies range from verifying a username and password, delegated authentication using OAuth or federated authentication using OpenID.

[passport.js](http://www.passportjs.org/)

**Note** 
in order for this file to work, it would need to be able to connect to your `models/` folder.

### **The `models/` folder**

```shell
models/
├── index.js
└── user.js

0 directories, 2 files
```

The `index.js` file contains the file system (`fs`) of the application that communicates with MYSQL Sequalize, and starts up the configuration of the enviorment created by package.json file. 

Seqalize will then run in `config` as an object. 

The `user.js` file contains data that authorizes a new user to create a unique login. These validation are enforced by the use of Sequalize `dataTypes` & `validate` syntax. 

```javascript  
User.addHook("beforeCreate"), function(user) {
user.password = bcrypt.hashSync(user.password, bcrypt.genSaltSync(10), null);
});
```
`bcrypt.genSaltSync(10)` specifies the times to generate the hash and, thus, how secure the password will be. 10 is a fairly common value that will allow the hashing to be secure.



