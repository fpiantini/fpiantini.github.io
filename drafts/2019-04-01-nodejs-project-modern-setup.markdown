---
layout: post
title:  "How to setup a NodeJS client project in a quite modern way (Linux)"
date:   2019-04-01 11:48:00 +0100
categories: javascript nodejs programming webpack web
---

In this page we well see how to setup a quite modern development for a frontend web application using tools like npm, webpack, babel, etc.

You can refer to the [jsmodernsetup][jsmodernsetup] repository on github to check the modification done on the variuos steps.

## Prerequisites, project creation and `webpack` installation

- Install a decent modern version of [nodejs][nodejs]

- Using `npm`, install [yarn][yarn] at global scope:

```
$ npm install --global yarn
...
$ _
```

- Create a directory for the project and enter it:

```
$ mkdir myproject
$ cd myproject
$ _
```

## Step 1: `npm init`

Initialize the project with `npm init`, entering all the requested data:

```
$ npm init
...
$ _
```
After this a `package.json` file containing the project information is created in the project directory. This file can be freely modified if some of the information entered was wrong.

Check the [step-1][repo-step-1] of the sample repo.


## Step 2: install webpack + webpack accessories

Install webpack + webpack accessories as development tools (use the `-D` option):

```
$ yarn add -D webpack webpack-cli webpack-dev-server html-webpack-plugin
...
$ _
```

NOTE: using `yarn` to manage packages, all the information about the installed modules are in the `yarn.lock` file, and all the modules are in the `node_modules` directory. If you are using some configuration control program (e.g. `git`) this directory shall not be placed under configuration control (e.g. add it to the `.gitignore` file). The `node_modules` directory can be recreated (e.g. after a fresh clone) using the `yarn install` command.

See the [step-2][repo-step-2] of the sample repo to check the modification introduced in this phase.

## Preparing a simple "webapp"

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

## Using webpack #1: generating the distribution from source

The first thing that webpack is able to do is to pack the source files in a deploy area. For the moment we prepare just the configuration necessary to copy the file reducing the processing to the bare minimum.

To work webpack needs a configuration file `webpack.config.js`.

```javascript
const path = require('path');

module.exports = {
    entry: './src/js/main.js',
    output: {
        path: path.resolve(__dirname, 'dist')
        filename: 'js/bundle.js',
    },
    plugins: [
    new HtmlWebpackPlugin({
        filename: 'index.html',
        template: './src/index.html'
    }),
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,
                use: {
                    'loader': 'babel-loader',
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            },
        ]
    },
};
```





## `webpack` configuration




To be completed


[repo-step-2]: https://to-be-completed
[repo-step-2]: https://github.com/fpiantini/jsmodernsetup/commit/d9bb525135c4cd0aaddec10a3360676e204df7a9
[repo-step-1]: https://github.com/fpiantini/jsmodernsetup/commit/8bcfd010851e4b967d90f2d70b8661cb97c990d0
[jsmodernsetup]: https://github.com/fpiantini/jsmodernsetup
[nodejs]: https://nodejs.org/
[yarn]: https://yarnpkg.com/
