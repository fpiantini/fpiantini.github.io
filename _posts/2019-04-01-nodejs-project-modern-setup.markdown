---
layout: post
title:  "How to setup a NodeJS client project in a quite modern way (Linux)"
date:   2019-04-01 11:48:00 +0100
categories: javascript nodejs programming webpack web
---

In this page we well see how to setup a quite modern development for a frontend web application using tools like npm, [webpack][webpack], [babel][babel], etc.

You can refer to the [jsmodernsetup][jsmodernsetup] repository on github to check the modification done on the variuos steps.

## Prerequisites, project creation and `webpack` installation

- Install a decent modern version of [nodejs][nodejs]

- Using `npm`, install [yarn][yarn] at global scope:

```
npm install --global yarn
```

- Create a directory for the project and enter it:

```
mkdir myproject
cd myproject
```

## Step 1: `npm init`

Initialize the project with `npm init`, entering all the requested data:

```
npm init
```
After this a `package.json` file containing the project information is created in the project directory. This file can be freely modified if some of the information entered was wrong.

Check the [step-1][repo-step-1] of the sample repo.


## Step 2: install webpack + webpack accessories

Install webpack + webpack accessories as development tools (use the `-D` option):

```
yarn add -D webpack webpack-cli webpack-dev-server html-webpack-plugin
```

NOTE: using `yarn` to manage packages, all the information about the installed modules are in the `yarn.lock` file, and all the modules are in the `node_modules` directory. If you are using some configuration control program (e.g. `git`) this directory shall not be placed under configuration control (e.g. add it to the `.gitignore` file). The `node_modules` directory can be recreated (e.g. after a fresh clone) using the `yarn install` command.

See the [step-2][repo-step-2] of the sample repo to check the modification introduced in this phase.

## Step 3: Preparing a simple "webapp"

Inside the project directory create an `src` directory and create the following `html` file inside it:

```html
<!doctype html>
<html>
  <head>
    <title>Getting Started</title>
  </head>
  <body>
    Hello World!
  </body>
</html>
```

Inside the `src` directory create a `js` directory and create inside it the `main.js` file with some javascript code:

```javascript
console.log('Random JS. The answer is... 42');
```

In the repo, check the [step-3][repo-step-3].

## Step 4: Using webpack #1. Generating the distribution from source

The first thing that webpack is able to do is to pack the source files (html, style sheet and javascript) in a release area that can be later used for deployment. To execute webpack commands we need a webpack configuration file named `webpack.config.js`. For the moment we prepare just the configuration necessary to copy only the files we have (`index.html` and `main.js`) reducing the processing to the bare minimum.

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/js/main.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'js/bundle.js'
    },
    plugins: [
        new HtmlWebpackPlugin({
            filename: 'index.html',
            template: './src/index.html'
        }),
    ],
};
```

- The `entry:` block shown the position of the javascript main file in the development tree
- The `output:` block indicates the name of the release area where the file shall be copied. In this case the generated files will be placed in the `dist` directory.
- The `HtmlWebpackPlugin` plugin is used to deploy also the html file. During this copy operation the html file is instrumented to call the javascript script (a `<script>` tag is added)

To generate the release files enter the following command:

```
npx webpack --mode development
```

Check the files in the `dist` directory and try to understand what happened.

If you are using a configuration control system, the `dist` directory shall be added to the list of the ignored items (e.g. if you are using `git`, add it to the `.gitignore` file).

For additional information, check the documentation of [webpack][webpack] and [HtmlWebpackPlugin][html-webpack-plugin] and the tons of examples and courses you can found online.

In the example repo, check the [step-4][repo-step-4].

## Step 5: Add some rules to the `package.json` file

The `package.json` file can be used to add shortcut to frequently used commands.
For example add the `"dev"` and `"build"` items in the `script` section of the file:

```json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack --mode development",
    "build": "webpack --mode production"
  },
```

After this, to build the distribution file in development mode use the command:

```
npm run dev
```

and to build in production mode, use the command:

```
npm run build
```

In the example repo, check the [step-5][repo-step-5].

## Step 6. Using webpack #2. The webpack development server

The [webpack development server][webpack-dev-server]is an useful tool that can be used during development to update in real-time the browser when a source file is modified. To use it the following steps shall be done:

- Add a `devServer` section in the `webpack.config.js`:

```javascript
    devServer: {
        contentBase: './dist'
    },
```
- Add a `start` shortcut in the `"script"` section of the `packages.json` file:

```json
  "start": "webpack-dev-server --mode development --open 'firefox'"
```

To start the development server enter the command:

```
npm run start
```

This starts the server to localhost on port 8080. A Firefox tab shall be automatically opened and the main page of the application should be shown. To stop server hot Ctrl-C in the terminal where you launch it.

NOTE: the argument to the `--open` option (`firefox` in the example) can be changed to to browser you prefer (a popular choice is `google-chrome`).

Check the [step-6][repo-step-6] in the repo.

## Step 7: Installing and using Babel

[Babel][babel] is a compiler that translates modern javascript code in a format understable by all browsers. This section explains how to install babel, and how to add the necessary components and configuration to use it inside webpack.

To install it enter the following command:

```
yarn add -D babel-loader@7 @babel/core @babel/preset-env
```

NOTE: `babel-loader`is the package which make it possible to use Babel in webpack.

To activate the `babel` compiler when a webpack command is entered, the following section shall be added to the `webpack.config.js` file:

```javascript
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            }
        ]
    }
```
Prepare a `.babelrc` configuration file with, for example, the following content:

```
{
    "presets": [
        ["env", {
            "targets": {
                "browsers": [
                    "last 5 versions",
                    "ie > 8"
                ]
            }
        }]
    ]
}
```

After this when we recompile our site using the `npm run dev` or `npm run build` the `bundle.js` generated in the `dist` is generated by babel taking into account the compatibility rules specified in `.babelrc`.

**NOTE:** with the advent of babel v. 7 some packages inside the npm system was renamed:

- `babel-core` becomes `@babel/core`
- `babel-preset-env` becomes `@babel/preset-env`

For this reason the command we gave to install babel use the new "@babel" components.
After this setup, during compilation using babel, it is possible that some errors are generated by packages that still refers to old packages sintax. For this reason can happen that the old babel packages needs to be added too. It is important to install the right version, so if you have this kind of problem (like we have in april 2019 when this guide was written), enter the following command to install the "compatibility" packages:

```
yarn add -D babel-core@^7.0.0-0 babel-preset-env@^7.0.0-0
```

In the example repo, check the [step-7][repo-step-7].


## Step 8: Installing the babel polyfill component

**NOTE:** this is an optional step, that shall be done only if your source code uses advanced javascript and HTML concepts that are not supported by all browser. 

From [wikipedia][wikipedia-polyfill]: _"In web development, a polyfill is code that implements a feature on web browsers that do not support the feature"._

Babel provides an implementation of a polyfill library that can be installed with the following command:

```
yarn add -D @babel/polyfill
```

The polyfill library can increase considerably the size of the generated `bundle.js` file. To avoid this only the necessary polyfill code shall be included in the final bundle. A way to do this, is to [use the `useBuiltIns` option][polyfill-usebuiltins] in the presets section of the `.babelrc` file:

```
    ["@babel/preset-env", {
        "useBuiltIns": "entry"
    }],
```

For additional info on usage of the polyfill library, check the babel documentation.

**NOTE:** also for the polyfill babel component there was a change of nomenclature when the v. 7 was released. For compatibility reasons it can happen that you have to install the old `babel-polyfill` package. In this case it is important to install the correct version:

```
yarn add -D babel-polyfill@\^7.0.0-0
```

In the example repo, check the [step-8][repo-step-8].

## Step 9: adding a CSS style sheet

In this step we add a CSS style sheet to the project.

First, the `style-loader` and `css-loader` components shall be added to the project (as development modules):

```
yarn add -D style-loader css-loader
```


Then we add a CSS style sheet to the project. In our sample project, we add a `style.css` file in the `src` directory with a minimal content, just to change the color of the text displayed in the page:

```css
* {
        color: red;
}
```

After this, the proper configuration section shall be added to the `webpack.config.js` file:

```javascript
...
module.exports = {
    ...
    module: {
        rules: [
            { test: /\.css$/, use: ['style-loader', 'css-loader'] }
        ]
    }
};
```

and an import line shall be added to the `src/js/main.js` file:

```javascript
import './style.css';
```

After this, if we restart the `webpack-web-server` and reopen the page, the text present in the web page will be colored in red.

See the [step-9][repo-step-9] in the example repo.

## Credits

- _["Un 'semplice' index.html nel 2018"][develer-blog-article]_ by Tommaso Visconti, Deverer blog, november 2018 (in italian language)


[jsmodernsetup]: https://github.com/fpiantini/jsmodernsetup
[nodejs]: https://nodejs.org/
[yarn]: https://yarnpkg.com/
[webpack]: https://webpack.js.org/
[babel]: https://babeljs.io/
[html-webpack-plugin]: https://github.com/jantimon/html-webpack-plugin
[webpack-dev-server]: https://github.com/webpack/webpack-dev-server
[wikipedia-polyfill]: https://en.wikipedia.org/wiki/Polyfill_(programming)
[polyfill-usebuiltins]: https://babeljs.io/docs/en/babel-preset-env#usebuiltins
[develer-blog-article]: http://develer-blog.tumblr.com/post/179853716064/un-semplice-indexhtml-nel-2018

[repo-step-1]: https://github.com/fpiantini/jsmodernsetup/commit/8bcfd010851e4b967d90f2d70b8661cb97c990d0
[repo-step-2]: https://github.com/fpiantini/jsmodernsetup/commit/d9bb525135c4cd0aaddec10a3360676e204df7a9
[repo-step-3]: https://github.com/fpiantini/jsmodernsetup/commit/9fdc7afe1d5bdc412f6aa722fe1004a4307da921
[repo-step-4]: https://github.com/fpiantini/jsmodernsetup/commit/f61ae944b72195818a1891f693bdd20d904f778c
[repo-step-5]: https://github.com/fpiantini/jsmodernsetup/commit/7263ab8b1d87f69fd01fab842f74463af61ea13f
[repo-step-6]: https://github.com/fpiantini/jsmodernsetup/commit/7f56952eb8566e174295389bf3bd11d9c7c73908
[repo-step-7]: https://github.com/fpiantini/jsmodernsetup/commit/35272b2d1b7b4c936eecbeda14c70bf44ff1739b
[repo-step-8]: https://github.com/fpiantini/jsmodernsetup/commit/7467c322d65d28f0e992d8ecf2ebd09152d601d7
[repo-step-9]: https://github.com/fpiantini/jsmodernsetup/commit/8a2a59a7bded2a77ba1ee1cf40e629a26b9fd629
