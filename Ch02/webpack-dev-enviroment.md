# Developent Environment Setup & Webpack Basics

![React 開發環境設置與 Webpack 入門教學](./images/react-webpack-browserify.png "React 開發環境設置與 Webpack 入門教學")

## Introduction
As the old Chinese saying goes, one must have good tools in order to do a good job. Writting programs is no exception -- a good development environment facilitates development. We will discuss two major development methods using React: CDN-based and [webpack] (https://webpack.github.io/) (We will not cover [TypeScript](https://www.typescriptlang.org/)). [browserify](https://webpack.github.io/) and [Gulp](http://gulpjs.com/) is in appendices. Readers shall be able to start developing with React after this chapter.

## JavaScript Modularization
As websites become more complex, they are more like interactive web applications than simple web pages. In order to solve problems such as global namspace pollution and difficulty to maintain, JavaScript has made great strides in modularization. You probably have heard about Javascript modularization tools like `Webpack`, `Browserify`, `module bundlers`, `AMD`, `CommonJS`, `UMD`, `ES6 Module`, etc. We have briefly touched upon the different modulization specifications in the last chapter. Those, who are not familiar with JavaScript modularized development, can refer to [This Article (Chinese)](http://huangxuan.me/2015/07/09/js-module-7day/) and [This Article](https://medium.freecodecamp.com/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#.oa2n5s5zt) to get some general idea.

There are three advantages developing web applications using modules:

1. Maintainability
2. Namespacing
3. Reusability

It's recommended that developers organize React applications using `Webpack` and `module bundlers`. However, in order to introduce readers to the idea of `React` (assuming our readers have experience with `JavaScript`, `jQuery`) without overwelming them with the powerful and complex functionalities, we start our introduction to React by using an example of `CDN`, which load React using `<script>`.

![React 開發環境設置與 Webpack 入門教學](./images/react.png "React 開發環境設置與 Webpack 入門教學")
使用 CDN-based 的開發方式缺點是較難維護我們的程式碼（當引入函式庫一多就會有很多 `<script/>`）且會容易遇到版本相容性問題，不太適合開發大型應用程式，但因為簡單易懂，適合教學上使用。

Below is an [offficial example ] of React (https://facebook.github.io/react/index.html), using `React v15.2.1`：

1. 理解 `React` 是 `Component` 導向的應用程式設計
2. 引入 `react.js`、`react-dom.js`（react 0.14 後將 react-dom 從 react 核心分離，更符合 react 跨平台抽象化的定位）以及 `babel-standalone` 版 script（可以想成 `babel` 是翻譯機，翻譯瀏覽器看不懂的 `JSX` 或 `ES6+` 語法成為瀏覽器看的懂得的 `JavaScript`。為了提昇效率，通常我們都會在伺服器端做轉譯，這點在 production 環境尤為重要）
3. 在 `<body>` 撰寫 React Component 要插入（mount）指定節點的地方：`<div id="example"></div>`
4. 透過 `babel` 進行語言翻譯 `React JSX` 語法，`babel` 會將其轉為瀏覽器看的懂得 `JavaScript`。其代表意義是：`ReactDOM.render(欲 render 的 Component 或 HTML 元素, 欲插入的位置)`。所以我們可以在瀏覽器上打開我們的 `hello.html`，就可以看到 `Hello, world!` 。That's it，我們第一個 `React` 應用程式就算完成了！

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
    <!-- 以下引入 react.js, react-dom.js（react 0.14 後將 react-dom 從 react 核心分離，更符合 react 跨平台抽象化的定位）以及 babel-core browser 版 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/15.2.1/react.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/15.2.1/react-dom.min.js"></script>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.18.1/babel.min.js"></script>
  </head>
  <body>
    <!-- 這邊的 id="example" 的 <div> 為 React Component 要插入的地方 -->
    <div id="example"></div>
    <!-- 以下就是包在 babel（透過進行語言翻譯）中的 React JSX 語法，babel 會將其轉為瀏覽器看的懂得 JavaScript -->
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

上面我們先簡單介紹了 CDN-based 的開發方式讓大家先對於 React 有個基本印象，但由於 CDN-based 的開發方式有不少缺點。所以接下來的 Webpack 將會是我們接下來範例的主要使用的開發工具。

[Webpack](https://webpack.github.io/) 是一個模組打包工具（module bundler），以下列出 Webpack 的幾項主要功能：

- 將 CSS、圖片與其他資源打包
- 打包之前預處理（Less、CoffeeScript、JSX、ES6 等）檔案
- 依 entry 文件不同，把 .js 分拆為多個 .js 檔案
- 整合豐富的 Loader 可以使用（Webpack 本身僅能處理 JavaScript 模組，其餘檔案如：CSS、Image 需要載入不同 Loader 進行處理）

接下來我們一樣透過 Hello World 實例來介紹如何用 Webpack 設置 React 開發環境：

1. 依據你的作業系統安裝 [Node](https://nodejs.org/en/) 和 [NPM](https://www.npmjs.com/)（目前版本的 Node 都會內建 NPM）

2. 透過 NPM 安裝 Webpack（可以 global 或 local project 安裝，這邊我們使用 local）、webpack loader、webpack-dev-server

	Webpack 中的 loader 類似於 browserify 內的 transforms，但 Webpack 在使用上比較多元，除了 JavaScript loader 外也有 CSS Style 和圖片的 loader。此外，`webpack-dev-server` 則可以啟動開發用 server，方便我們測試

	```
	// 按指示初始化 NPM 設定檔 package.json
	$ npm init 
	// --save-dev 是可以讓你將安裝套件的名稱和版本資訊存放到 package.json，方便日後使用
	$ npm install --save-dev babel-core babel-eslint babel-loader babel-preset-es2015 babel-preset-react html-webpack-plugin webpack webpack-dev-server
	```

3. 在根目錄設定 `webpack.config.js`

	事實上，`webpack.config.js` 有點類似於 `gulp` 中的 `gulpfile.js` 功用，主要是設定 `webpack` 的相關設定

	```javascript
	// 這邊使用 HtmlWebpackPlugin，將 bundle 好的 <script> 插入到 body。${__dirname} 為 ES6 語法對應到 __dirname  
	const HtmlWebpackPlugin = require('html-webpack-plugin');

	const HTMLWebpackPluginConfig = new HtmlWebpackPlugin({
	  template: `${__dirname}/app/index.html`,
	  filename: 'index.html',
	  inject: 'body',
	});
	
	module.exports = {
	  // 檔案起始點從 entry 進入，因為是陣列所以也可以是多個檔案
	  entry: [
	    './app/index.js',
	  ],
	  // output 是放入產生出來的結果的相關參數
	  output: {
	    path: `${__dirname}/dist`,
	    filename: 'index_bundle.js',
	  },
	  module: {
	  	// loaders 則是放欲使用的 loaders，在這邊是使用 babel-loader 將所有 .js（這邊用到正則式）相關檔案（排除了 npm 安裝的套件位置 node_modules）轉譯成瀏覽器可以閱讀的 JavaScript。preset 則是使用的 babel 轉譯規則，這邊使用 react、es2015。若是已經單獨使用 .babelrc 作為 presets 設定的話，則可以省略 query
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
	  // devServer 則是 webpack-dev-server 設定
	  devServer: {
	    inline: true,
	    port: 8008,
	  },
	  // plugins 放置所使用的外掛
	  plugins: [HTMLWebpackPluginConfig],
	};
	```

4. 在根目錄設定 `.babelrc`

	```javascript 
	{
	  "presets": [
	    "es2015",
	    "react",
	  ],
	  "plugins": []
	}
	```

5. 安裝 react 和 react-dom

	```
	$ npm install --save react react-dom
	```

6. 撰寫 Component（記得把 `index.html` 以及 `index.js` 放到 `app` 資料夾底下喔！）
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
		<!-- 欲插入 React Component 的位置 -->
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

7. 在終端機使用 `webpack` 進行成果展示，webpack 相關指令：

	- webpack：會在開發模式下開始一次性的建置
	- webpack -p：會建置 production 的程式碼 
	- webpack --watch：會監聽程式碼的修改，當儲存時有異動時會更新檔案
	- webpack -d：加入 source maps 檔案
	- webpack --progress --colors：加上處理進度與顏色

	如果不想每次都打一長串的指令碼的話可以使用 `package.json` 中的 `scripts` 設定

	```javascript 
	"scripts": {
	  "dev": "webpack-dev-server --devtool eval --progress --colors --content-base build"
	}
	```

	然後在終端機執行：

	```
	$ npm run dev
	```

Opening your browser to `http://localhost:8008`, you will see `Hello, world!`.

## Summary
Above is how we set up React development environment using Webpack. Readers are encouraged to follow the instructions.看到這邊的讀者不妨自己動手設定開發環境，體驗一下 React 開發環境的感覺，畢竟若是只有閱讀文字的話很容易就會忘記喔！若你不想在環境設定上花太多時間的話，不妨參考 Facebook 開發社群推出的 [create-react-app](https://github.com/facebookincubator/create-react-app)，可以快速上手，使用 Webpack、[Babel](https://babeljs.io/)、[ESLint](http://eslint.org/) 開發 React 應用程式。We will cover React/JSX/Component in the next chapter. 

## Further Reading
1. [JavaScript 7 day seminar](http://huangxuan.me/2015/07/09/js-module-7day/)
2. [History of Web Frontend Modularization](https://github.com/seajs/seajs/issues/588)
3. [Webpack Tutorial (Chinese)](http://zhaoda.net/webpack-handbook/index.html)
4. [WEBPACK DEV SERVER](http://webpack.github.io/docs/webpack-dev-server.html)

(image via [srinisoundar](https://cdn-images-1.medium.com/max/477/1*qhI4E_g3TDOK0uu1VAJlCQ.png)、[sitepoint](https://d2sis3lil8ndrq.cloudfront.net/screencasts/46e215cd-2eb3-4cf0-b699-713977a2b644.png)、[keyholesoftware](https://keyholesoftware.com/wp-content/uploads/Browserify-5.png)、[survivejs](http://survivejs.com/webpack/images/webpack.png))

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: Introduction to React Ecosystem](https://github.com/druckenclam/reactjs101/blob/en/Ch01/react-ecosystem-introduction.md) | [Next Chapter: Introduction to React/JSX/Component](https://github.com/druckenclam/reactjs101/blob/en/Ch03/reactjs-introduction.md) |

| [Correction, Questions, and Wish List](https://github.com/kdchang/reactjs101/issues) |
