# Developent Environment Setup & Webpack Basics

![React 開發環境設置與 Webpack 入門教學](./images/react-webpack-browserify.png "React 開發環境設置與 Webpack 入門教學")

## Introduction
As the old Chinese saying goes, one must have good tools in order to do a good job. Writting programs is no exception -- a good development environment facilitates development. We will discuss two major development methods using React: CDN-based and [webpack] (https://webpack.github.io/) (We will not cover [TypeScript](https://www.typescriptlang.org/)). [browserify](https://webpack.github.io/) and [Gulp](http://gulpjs.com/) is in appendices. Readers shall be able to start developing with React after this chapter.

## JavaScript Modularization
As websites become more complex, they are more like interactive web applications than simple web pages. In order to solve problems such as global namspace pollution and difficulty to maintenance, JavaScript has made great strides in modularization. You probably have heard about Javascript modularization tools like `Webpack`, `Browserify`, `module bundlers`, `AMD`, `CommonJS`, `UMD`, `ES6 Module`, etc. We have briefly touched upon the different modulization specifications in the last chapter. For those who are not familiar with JavaScript modularized development, can refer to [This Article (Chinese)](http://huangxuan.me/2015/07/09/js-module-7day/) and [This Article](https://medium.freecodecamp.com/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#.oa2n5s5zt) to get some general idea.

There are three advantages developing web applications using modules:

1. Maintainability
2. Namespacing
3. Reusability

It's recommended that developers organize React applications using `Webpack` and `module bundlers`. However, in order to introduce readers to the idea of `React` (assuming our readers have experience with `JavaScript`, `jQuery`) without overwelming them with the powerful and complex functionalities, we start our introduction to React by using an example of `CDN`, which load React using `<script>`.

![React 開發環境設置與 Webpack 入門教學](./images/react.png "React 開發環境設置與 Webpack 入門教學")
CDN-based methods posts a challenge for code maintenance, as there are multiple `<script/>`）tags. There is no dependency or version management either. Though not a good fit for applications of large scale, it's simple and good for teaching and learning purpose.

Below is an [offficial example ] of React (https://facebook.github.io/react/index.html), using `React v15.2.1`：

1. Understand `React` applications are build upon `Component`
2. Introduce `react.js`、`react-dom.js`（from react 0.14 onward, react-dom is separated from react core, making React more platform independent) and `babel-standalone` (`babel` can be thought of as a translation machine, translating `JSX` and `ES6+` into `JavaScript` that browsers understand. For performance, the translation is always done on the server side. This is especially important for production.
3. Specify a mounting point in the `<body>` for React Components to mount:`<div id="example"></div>`
4. `babel` transpiles `React JSX` into `JavaScript` that browsers understand. `ReactDOM.render` rende the Component at the mounting point. Opening our browser to `hello.html`, it will show `Hello, world!`. That's it，we just finished our first `React` application!

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
    <!-- Introduce react.js, react-dom.js (react 0.14 separate react-dom from react core, to make react core platform independent) and babel-core for browsers -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/15.2.1/react.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/15.2.1/react-dom.min.js"></script>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.18.1/babel.min.js"></script>
  </head>
  <body>
    <!-- <div> identified by id="example" is the mounting point for React Component -->
    <div id="example"></div>
    <!-- Using babel to transpile React JSX to JavaScript that browsers support -->
    <script type="text/babel">
      ReactDOM.render(
        <h1>Hello, world!</h1>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

Result in the browser:

![React 開發環境設置與 Webpack 入門教學](./images/hello-world.png "React 開發環境設置與 Webpack 入門教學")

## Webpack
![React 開發環境設置與 Webpack 入門教學](./images/webpack-module-bundler.png "React 開發環境設置與 Webpack 入門教學")

Now we know how to use React with CDN-based approach，Due to the drawbacks of CDN-based development, we will focus on Webpack for the remainder of the book.

[Webpack](https://webpack.github.io/) is a module bundler. Among other things, below is its major functionalities:

- bundle CSS, images, and other resources;
- bundle preprocessed files written in Less, CoffeeScript, JSX, and ES6;
- based on entry files, split one .js file into multiple ones;
- Integrate multiple loaders (Webpack core can only handles JavaScript. Other files such as CSS and Image need separate loaders).

Next up, we learn how to use React with `webpack` through a Hello World example:

1. Install [Node](https://nodejs.org/en/) 和 [NPM](https://www.npmjs.com/) (Node normally include NPM)

2. Install Webpack via NPM (either global or local, we use local installation) webpack loader, and webpack-dev-server

	Webpack's loaders are similar to browserify's transforms, yet loders have different usages: there are loaders for JavaScript, loaders for CSS Style, and loaders for images. `webpack-dev-server` is a builtin web server for testing purpose.

	```
	// NPM initialization -- creates package.json
	$ npm init 
	// --save-dev save packages and their versions into package.json for future development
	$ npm install --save-dev babel-core babel-eslint babel-loader babel-preset-es2015 babel-preset-react html-webpack-plugin webpack webpack-dev-server
	```

3. At your root folder `webpack.config.js`

	In fact, `webpack.config.js`, similar to `gulpfile.js` of `gulp`,  manges settings for `webpack`.

	```javascript
	// Use HtmlWebpackPlugin to place bundled script into <body>. ${__dirname} is ES6 syntax for __dirname  
	const HtmlWebpackPlugin = require('html-webpack-plugin');

	const HTMLWebpackPluginConfig = new HtmlWebpackPlugin({
	  template: `${__dirname}/app/index.html`,
	  filename: 'index.html',
	  inject: 'body',
	});
	
	module.exports = {
	  // entry point. It's an array so it could have multiple entries
	  entry: [
	    './app/index.js',
	  ],
	  // output specify where to place the bundled Javascript
	  output: {
	    path: `${__dirname}/dist`,
	    filename: 'index_bundle.js',
	  },
	  module: {
	  	// loaders specify all the loaders. We use babel-loader to transpile all .js file (filtered by the regular expression excluding npm packages) into JavaScript that browsers understand. preset specify babel transpilation rules -- react、es2015. If .babelrc has presets setting, presents here can be omitted.
	    loaders: [
	      {
	        test: /\.js$/,
	        exclude: /node_modules/,
	        loader: 'babel-loader',
	        query: {
	          presets: ['es2015', 'react'],
	        },
	      },
	    ],
	  },
	  // devServer specifies webpack-dev-server
	  devServer: {
	    inline: true,
	    port: 8008,
	  },
	  // plugins specify webpack plugins
	  plugins: [HTMLWebpackPluginConfig],
	};
	```

4. set up `.babelrc` in root folder

	```javascript 
	{
	  "presets": [
	    "es2015",
	    "react",
	  ],
	  "plugins": []
	}
	```

5. install react and react-dom

	```
	$ npm install --save react react-dom
	```

6. Write Component (don't forget to place `index.html` and `index.js` under `app` folder)
	`index.html`

	```html 
	<!DOCTYPE html>
	<html lang="en">
	<head>
		<meta charset="UTF-8">
		<title>React Setup</title>
		<link rel="stylesheet" type="text/css" href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
	</head>
	<body>
		<!-- mounting point for React Component -->
		<div id="app"></div>
	</body>
	</html>
	```

	`index.js`

	```js 
	import React from 'react';
	import ReactDOM from 'react-dom';

	class App extends React.Component {
	  constructor(props) {
	    super(props);
	    this.state = {
	    };
	  }
	  render() {
	    return (
	      <div>
	        <h1>Hello, World!</h1>
	      </div>
	    );
	  }
	}

	ReactDOM.render(<App />, document.getElementById('app'));
	```

7. `webpack` CLI commands:

	- webpack: build code for development environment
	- webpack -p: build code for production environment 
	- webpack --watch: enable hot reload
	- webpack -d: add source maps
	- webpack --progress --colors: display progress bar and color

	The commands can be integrated into section `scripts` of `package.json`.

	```javascript 
	"scripts": {
	  "dev": "webpack-dev-server --devtool eval --progress --colors --content-base build"
	}
	```

	Run it in a terminal:

	```
	$ npm run dev
	```

Opening your browser to `http://localhost:8008`, you will see `Hello, world!`.

## Summary
Above is how we set up React development environment using Webpack. Readers are encouraged to follow the instructions. It help you learn and understand the concept. Readers can also refer to [create-react-app] (https://github.com/facebookincubator/create-react-app) by Facebook community. It creates a template for development using Webpack, [Babel] (https://babeljs.io/), [ESLint] (http://eslint.org/). We will cover React/JSX/Component in the next chapter. 

## Further Reading
1. [JavaScript 7 day seminar (Chinese)](http://huangxuan.me/2015/07/09/js-module-7day/)
2. [History of Web Frontend Modularization (Chinese)](https://github.com/seajs/seajs/issues/588)
3. [Webpack Tutorial (Chinese)](http://zhaoda.net/webpack-handbook/index.html)
4. [WEBPACK DEV SERVER](http://webpack.github.io/docs/webpack-dev-server.html)

(image via [srinisoundar](https://cdn-images-1.medium.com/max/477/1*qhI4E_g3TDOK0uu1VAJlCQ.png)、[sitepoint](https://d2sis3lil8ndrq.cloudfront.net/screencasts/46e215cd-2eb3-4cf0-b699-713977a2b644.png)、[keyholesoftware](https://keyholesoftware.com/wp-content/uploads/Browserify-5.png)、[survivejs](http://survivejs.com/webpack/images/webpack.png))

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: Introduction to React Ecosystem](https://github.com/druckenclam/reactjs101/blob/en/Ch01/react-ecosystem-introduction.md) | [Next Chapter: Introduction to React/JSX/Component](https://github.com/druckenclam/reactjs101/blob/en/Ch03/reactjs-introduction.md) |

| [Correction, Questions, and Wish List](https://github.com/kdchang/reactjs101/issues) |
