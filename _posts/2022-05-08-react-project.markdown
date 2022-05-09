---
layout: post
title:  React project
date:   2022-05-08 10:21:22 -0700
categories: web
---

This post captures notes about the React web project. 

[Set up][1]

# Set up 

Here are the steps to start the React project from scratch.  

First, install `react` and `react-dom`

```bash
# Step 1 - Create a project folder, run npm init -y
npm init -y

# Step 2 - Install react and react dom
npm install react react-dom

# Create a git ignore file, and add the following to the ignore file
node_modules
.DS_Store
dist
.idea/


# Install babel, webpack loader and plugin

npm install --save-dev @babel/core @babel/preset-env @babel/preset-react webpack webpack-cli webpack-dev-server babel-loader css-loader style-loader html-webpack-plugin
 
```

The above code will generate the `package.json`. 

Modify the `package.json` and `package-lock.json` to set up webpack-dev-server 

A sample `package.json`

```json
{
  "name": "sportdashboard-web",
  "version": "1.0.0",
  "description": "Board for Sport bets",
  "main": "index.js",
  "scripts": {
    "build": "webpack",
    "start": "webpack serve"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/HaiLe/sportdashboard.git"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/HaiLe/sportdashboard/issues"
  },
  "homepage": "https://github.com/HaiLe/sportdashboard#readme",
  "babel": {
    "presets": [
      "@babel/preset-env",
      "@babel/preset-react"
    ]
  },
  "dependencies": {
    "prop-types": "^15.8.1",
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "react-icons": "^4.3.1",
    "webpack-dev-server": "^3.11.0"
  },
  "devDependencies": {
    "@babel/core": "^7.17.0",
    "@babel/preset-env": "^7.16.11",
    "@babel/preset-react": "^7.16.7",
    "babel-loader": "^8.2.3",
    "css-loader": "^6.6.0",
    "html-webpack-plugin": "^5.5.0",
    "style-loader": "^3.3.1",
    "webpack": "^5.68.0",
    "webpack-cli": "^4.9.2",
    "webpack-dev-server": "^3.11.0"
  }
}
```

and add the `webpack.config.js` to the root directory

```js
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: '/app/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'index_bundle.js'
    },
    module: {
        rules: [
            { test: /\.(js)$/, use: 'babel-loader' },
            { test: /\.css$/, use: [ 'style-loader', 'css-loader' ]}
        ]
    },
    mode: 'development',
    plugins: [
        new HtmlWebpackPlugin({
            template: 'app/index.html'
        })
    ]
}
```

You can view the sample project here: [https://github.com/HaiLe/sportdashboard][2]

Once the set up step is done.  You’re ready to add `index.js` and `index.html` file to the project. 






[1]:	#set-up
[2]:	https://github.com/HaiLe/sportdashboard