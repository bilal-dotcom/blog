---
title: "CREATE A PROJECT WITH REACT AND TAILWIND CSS v2.0"
seoTitle: "React & Tailwind CSS v2.0 Project Guide"
seoDescription: "Create a React project with Tailwind CSS: a step-by-step guide on setup and configuration"
datePublished: Thu Jan 21 2021 19:11:41 GMT+0000 (Coordinated Universal Time)
cuid: ckk78ef6c00i7h2s12x4lfwv3
slug: create-a-project-with-react-and-tailwind-css-v20
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1611248552732/JJLQGOiwh.jpeg
tags: developer, reactjs, tailwind, tailwind-css

---

Hi everyone. For today, i will show you how to create a React project using TailwindCSS. Soo, let's start 👍😀. Tailwind is an incredible CSS framework packed with classes like flex, pt-4, text-center and rotate-90 that can be composed to build any design, directly in your markup.

## Start a React project

To have the React framework available, you firstly need to run:

```plaintext
npm install -g create-react-app
```

Then, you can create your React app using:

```plaintext
npx create-react-app project_name
```

Now you have created your React app and you can start it by running:

```plaintext
npm start
```

## Setting Up Tailwind CSS

What we will do right now, is to set up Tailwind CSS in our react project. You can watch the video tutorial here.

First of all, we need to install the differents packages of tailwindcss and his dependencies.

To install tailwind, you have to run:

```plaintext
npm install tailwindcss postcss autoprefixer postcss-cli -D
```

With this command, you install npm packages, using by Tailwind, which are `PostCSS`, `autoprefixer` and `postcss-cli`. The `-D` means dev dependencies .Here postcss is used for compiling tailwindcss and also allows us to use autoprefixer.

Now open src folder and create a folder inside named `assets` and create two CSS files in it named `style.css` and `tailwind.css`. Add the below code in `tailwind.css`:

```plaintext
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Now, you have to iniatlize tailwind default configuration by running:

```plaintext
npx tailwindcss init --full
```

This commande will generate a `tailwind.config.js` file. This file contains all tailwind codes and configurations by default. You can tchek it and view a lot of code CSS.

Then, you should create two files in which you can manage your tailwind css configurations like colors, fonts, and much more.

```plaintext
npx tailwindcss init tailwindcss-config.js -p
```

This command will generate `tailwindcss-config.js` and `postcss.config.js`. Open the `tailwindcss-config.js` and add:

```plaintext
module.exports = {
  purge: [],
  darkMode: false, // or 'media' or 'class'
  theme: {
    extend: {},
  },
  variants: {
    extend: {},
  },
  plugins: [],
}
```

Now you have to modify the plugins of your `postcss.config.js` by adding the tailwindcss plugins. The `postcss.config.js` file should look like this.

```plaintext
module.exports = {
  plugins: {
    tailwindcss: { config: './tailwindcss-config.js' },
    autoprefixer: {},
  },
}
```

Now go to package.json as we want to create some extra scripts. Replace all the script files with the following.

```plaintext
  "scripts": {
    "start": "npm run watch:css && react-scripts start",
    "build": "npm run watch:css && react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "watch:css": "postcss src/assets/tailwind.css -o src/assets/style.css"
  }
```

The last thing you have to do is to import the `style.css` in your `app.js` file

```plaintext
import './assets/style.css'
```

Now run `npm start` and enjoy. You have now install TailwindCSS in your project and it is ready to be used. Read the documentation of [TailwindCSS](https://tailwindcss.com/docs/container) to know more about customizing . You can also try your code [here](https://play.tailwindcss.com/6F2VruLary) to play with TailwindCSS.

### THANKS FOR READING 😃

If you have found this helpful, don't hesitate to let a comment or like 👍. You can also read my posts [here](https://blog.fikara.io) .