---
title: "Create A Project With Node, Express And Ejs"
seoTitle: "Node, Express, and EJS Project Guide"
seoDescription: "Learn how to create a project using Node.js, Express, and EJS template engine with this step-by-step guide"
datePublished: Thu Jan 14 2021 01:21:46 GMT+0000 (Coordinated Universal Time)
cuid: ckjw63jlp0aneces19enxcc9m
slug: create-a-project-with-node-express-and-ejs
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1610587890519/XXCXp-SoX.png
tags: express, project, nodejs

---

For my first blog here, i will show you how to create a project with node, express and ejs. Soo, let's start 👍😀.

### **Pre-requisites:**

* An code editing software
    
* Basic npm/node knowledge.
    
* Basic JavaScript and HTML knowledge
    

## INITIALIZE THE PROJECT

First of all, you have to create a folder `myNodeProject` that will content all the files you need. Using your code editing, do the following:

```plaintext
mkdir myNodeProject
cd myNodeProject
```

Now you have created your folder and you are inside. Then you need to initialize your node project with:

```plaintext
npm init
```

Once done, you will answer a series of questions about the project (e.g. name, description, version, author, etc.). A file named `package.json` will automatically be created with all the information. Look like this :

```plaintext
{
  "name": "myNodeProject",
  "version": "1.0.0",
  "description": "Project Node",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "FIKARA Bilal",
  "license": "ISC"
}
```

## INSTALL EXPRESS

Now we have to install `Express`, a framework for application development, Node.js:

```plaintext
npm install express --g
```

With this commande, the module is installed but is not yet in the project dependencies inside the `package.json file`. You can go and check it. But close the `package.json` before continuing.

```plaintext
npm install express --save
```

The module will now be visible in the package.json file,that means we are ready to build the server and test it in the browser.

## CREATE YOUR INDEX.JS FILE

Now you have to create your `index file` by running:

```plaintext
touch index.js
```

Your `index.js` is now created and you should get the express module we installed with the command: `npm install express`.  
Add the following to use this module to create our server referenced in the named variable, `app` :

```plaintext
const express = require('express');
const app = express();
```

Now let's specify the basic routing of our site. With the app.get () method, you specify a callback function that will be invoked whenever there is an HTTP GET request with the path ('/') relative to the site root.  
`Req` request the web page. `Res.send` sends "Hello world" in `res`.

```plaintext
app.get('/', (req, res) => {
    res.send('Hello world!!');
});

 app.listen(3000, () => {
    console.log("The application is running on the port 3000");
});
```

Start the server as follows, with the command:

```plaintext
node index.js
```

If you did everything correctly, you should see a page that says `"Hello world".` To stop the server, you just have to use: `CTRL + C`

## INSTALL EJS

We will install now EJS, a template engine for Node which will allow our server to select a view and send the information to be presented in an html document. To install `EJS`, run the following:

```plaintext
npm install ejs --g
```

Ejs is now installed, but not already in your `package.json`. Once again be sure to close `package.json` and run:

```plaintext
npm install ejs --save
```

Now you will be able to tchek EJS in the `package.json`.

```plaintext
 "dependencies": {
    "ejs": "^3.1.5",
    "express": "^4.17.1",
  }
```

Once `EJS` is installed, we need to create a new directory to store the `ejs` files. You can create a new directory, and a`ejs` file, as follows:

```plaintext
mkdir views
touch views/index.ejs
```

Now use your HTML knowledge to create a simple page. You can add the following content to your `index.ejs` file:

```plaintext
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NodeProject</title>
</head>
<body>
    <h1 >My First NodeProject</h1>
    <p>Hello!!!!</p>

</body>
</html>
```

Now, if we start the server again, you want to have this new code right?? Remember how we get our `"Hello world".` before.  
Yes, you should move back into `index.js` and use the `render()` method instead of `send()`

```plaintext
 app.get('/', (req, res) => {
    res.render('index');
});
```

Now you will see your new code as:

> ### My First NodeProject
> 
> Hello !!!!

## USE THE STATIC FILES

If you want your web page to look professional, you need to be able to use static files like style sheets or scripts. For this, you can use the statics files with the `use` method.

Create a new directory named `public` to the project with a CSS file inside.

mkdir public  
touch public/style.css

In this CSS file, add a simple code you want

```plaintext
h1{
   color: red:
};
p{
   text-align:center
}
```

Make sure that the public directory will be usable (therefore app.use) in the project.  
The `app.use` will permit to use the `static files` in the folder named `public`

```plaintext
app.set('views','./views'); 
app.set('view engine','ejs');
app.use('/public', express.static('public'));

app.get('/',(req,res)=>{
    res.render('index');
})
```

If you want this code to work, don' forget to add the `CSS link` into `index.ejs`

```plaintext
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" type="text/css" href="../public/style.css">
    <title>NodeProject</title>
</head>
```

Now reload the page to make sure it works 👍:

node index.js

## CONCLUSION

Now you can create your own NodeProject and build any page you want by using `ejs template`.