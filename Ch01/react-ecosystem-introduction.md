# Introduction to React Ecosystem

![React 生態系（Ecosystem）入門簡介](./images/react-eco-wp.gif "React 生態系（Ecosystem）入門簡介")

As per [React official website](https://facebook.github.io/react/), React is a JavaScript library specializing in UI (View). Since Facebook opened React source code in 2013, the ecosystem has been booming. By going through the ecosystem, we shall be able to understand some of the most important concepts in mordern web development, for example, modularization, ES6+, Webpack, Babel, ESLink, and fucntional programming, and to eventually become a better developer. 

## ReactJS
ReactJS is a Javascript Framework by Facebook. From the perspective of MVC, React represents view. Since ReactJS 0.14, react-dom have been separate from ReactJS, making ReactJS more focused and more in line with its paradigm that `Learn once, write everywhere`. Since ReactJS APIs are lean and target core functionality only, ReactJs applications need to work with its immense ecosystem, making learning React a long and winding road. If you are to fully utilized React in your applications, you must have a good command of the entire React Stack.

## JSX 
JSX is not a brand new language, but syntatic sugar(https://en.wikipedia.org/wiki/Syntactic_sugar), an [XML](https://zh.wikipedia.org/wiki/XML) like extension to ECMAScript. In JSX HTML tags and Javascript code are intertwined -- a stark contrast to the prior notion that HTML and JavaScript should be separated. Although it is possible to use React without JSX, I am sure that you will appreciate JSX when you start writing React Components.

## NPM
NPM（Node Package Manager）is a mainstream Node.js package manager. With NPM, you have a multitude of packages at your fingertips. Instead of reinventing the wheels, you choose ready-made packages with NPM commands. NPM packages are [CommonJS](https://en.wikipedia.org/wiki/CommonJS) compliant, so it must be used together with tools like Browserify for frontend engineering.然而因 NPM 是基於 Nested Dependency Tree，不同的套件有可能會在引入依賴時會引入相同但不同版本的套件，造成檔案大小過大的情形。這和另一個套件管理工具 [Bower](https://bower.io/) 專注在前端套件且使用 Flat Dependency Tree（讓使用者決定相依的套件版本）是比較不同的地方。

## ES6+
[ES6+](https://babeljs.io/blog/2015/06/07/react-on-es6-plus) refers to ES6 (ES2015), ES7 and any ECMAScript standards afterwards. ES6+ introduce a set of engrossing features and functionalities to the language, fixing lots of drawbacks of the old standards. React supports ES6+, so it's only sensible that we learn ES6+. All the example code in this book is written in ES6+.

## Babel
Not every browser supports ES6+. That's when [Babel](https://babeljs.io/), a JavaScript transpiler, comes in handy. It transpiles ES6+, JSX code into Javascript code that browsers support. The Babel configure `.babelrc` usually reside in the root folder. It specifies translation rules `preset`, and plugins.

## JavaScript Modularization
With web applications growing in size and complexity, JavaScript modularization is inevitable.  Due to the lack of official standards, many communities came up with their standards and practice. Below is a summary of some of the JavaScript modularization standards.

1. CDN-Based
	
	The tranditional way of including Javascript code via `<script>` tag is easy and simple, but it brings about a couple of problems:

	- It polutes the global scope and lead to potential conflicts
	- Files are loaded based on the order they appear in `<script>` tags without flexibility
	- It's hard to maintain in large applications
	- Users must make sure all dependencies are met

2. AMD

	[Asynchronous Module Definition](https://en.wikipedia.org/wiki/Asynchronous_module_definition), or AMD for short，is an asynchronous module loading specifization. it specify its dependencies when a module is declared. AMD is usually used for applications for browsers. The most famous application using AMD is [RequireJS](http://requirejs.org/)

	Basic Format:
	```js 
	define(id?, dependencies?, factory);
	```

3. CommonJS

	[CommonJS](http://wiki.commonjs.org/wiki/CommonJS) is a synchronous loading specification, which uses `require` to load modules and `exports`、`module.exports` to export modules. It is implemented by [Node.js](https://nodejs.org/en/) to develop server side applications and by [Browserify](http://browserify.org/) to develop browser side applications.

4. CMD

	CMD, [Common Module Definition](https://github.com/cmdjs/specification/blob/master/draft/module.md), is similar to AMD，but simpler，and compatible with CommonJS. 其最大特色為：依賴就近，延遲執行。主要實現為：[Sea.js](http://seajs.org/docs/#intro)。

5. UMD

	[Universal Module Definition](https://github.com/umdjs/umd) amis to be compatible with both CommonJS and AMD in the hope that applications will work across platforms.

6. ES6 Module

	ECMAScript6 make modularization part of the standard. As a reuslt, large scale JavaScript applications are more manageable. It will replace other specifications such as AMD and CommonJS. 成為通用於瀏覽器端和伺服器端的模組化解決方案。 The support is still lacking by browsers and Node. Applications using ES6 Module must be transpiled by [Babel](https://babeljs.io/) first.

## Webpack/Browserify + Gulp
隨著網頁應用程式開發的複雜性提昇，現在的網頁往往不單只是單純的網頁，而是一個網頁應用程式（WebApp）。為了管理複雜的應用程式開發，此時模組化開發方法便顯得日益重要，而理想上的模組化開發工具一直是前端工程的很大的議題。Webpack 和 Browserify + Gulp 則是進行 React 應用程式開發常用的開發工具，可以協助進行自動化程式碼打包、轉譯等重複性工作，提昇開發效率。本書範例主要會搭配 Webpack 進行開發。

1. Webpack

	[Webpack](https://webpack.github.io/) is a module bundler. Among other things, below is its major functionalities:
	- bundle CSS, images, and other resources
	- 打包之前預處理（Less、CoffeeScript、JSX、ES6 等）的檔案
	- based on entry files, split one .js file into multiple ones
	- 整合豐富的 Loader 可以使用（Webpack 本身僅能處理 JavaScript 模組，其餘檔案如：CSS、Image 需要載入不同 Loader 進行處理）

2. Browserify

	如同官網上說明的：`Browserify lets you require('modules') in the browser by bundling up all of your dependencies.
	`，Browserify 是一個可以讓你在瀏覽器端也能使用像 Node 用的 [CommonJS](https://en.wikipedia.org/wiki/CommonJS) 規範一樣，用輸出（export）和引用（require）來管理模組。此外，也能讓前端使用許多在 NPM 中的模組。

3. Gulp

	`Gulp` 是一個前端任務工具自動化管理工具（Task Runner）。隨著前端工程的發展，我們在開發前端應用程式時有許多工作是必須重複進行，例如：打包文件、uglify、將 LESS 轉譯成一般的 CSS 的檔案，轉譯 ES6 語法等工作。若是使用一般手動的方式，往往會造成效率的低下，所以透過像是 [Grunt](http://gruntjs.com/)、Gulp 這類的 Task Runner 不但可以提昇效率，也可以更方便管理這些任務。由於 Gulp 是透過 pipeline 方式來處理檔案，在使用上比起 Grunt 的方式直觀許多，所以這邊我們主要討論的是 Gulp。

## ESLint
[ESLint](http://eslint.org/) 是一個提供 JavaScript 和 JSX 的程式碼檢查工具，可以確保團隊的程式碼品質。其支援可插拔的特性，可以根據需求在 `.eslintrc` 設定檢查規則。目前主流的檢查規則會使用 Airbnb 所釋出的 [Airbnb React/JSX Style Guide](https://github.com/airbnb/javascript/tree/master/react)，在使用上需先安裝 [eslint-config-airbnb](https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb) 等套件。

## React Router
[React Router](https://github.com/reactjs/react-router) is a popular React Routing library， It maps URLs to components, which is quite useful for single page applications.

## Flux/Redux
[Flux](https://facebook.github.io/flux/) 是一個實現單項流的應用程式資料架構（architecture），同樣是由 Facebook 推出，並和 React 專注於 View 的部份形成互補。而由 Dan Abramov 所開發的 [Redux](https://github.com/reactjs/redux) 被 React 開發社群認為是 Flux-like 更優雅的作法，也是目前主流搭配 React 的狀態（State）管理工具。讓你在開發複雜的應用程式時可以更方便管理你的狀態（state）。

## ImmutableJS
[ImmutableJS](https://facebook.github.io/immutable-js/)，是一個能讓開發者建立不可變資料結構的函式庫。建立不可變（immutable）資料結構不僅可以讓狀態可預測性更高，也可以提昇程式的效能。

## Isomorphic JavaScript
Isomorphic JavaScript 是指前後端（Client/Server）共用相同部分的程式碼，讓 JavaScript 應用可以同時執行在瀏覽器端和伺服器端，在 React 中可以透過伺服器端渲染（server side rendering）靜態 HTML 的方式達到 Isomorphic JavaScript 效果，讓 SEO 和執行效能更加提昇並讓前後端共用程式碼。而另一個常一起出現的 Universal JavaScript 一般定義更為廣泛，係指可以運行在不同環境下的 JavaScript Code，並不局限於瀏覽器和伺服器端。但要留意的是在 Github 和許多技術文章的分享上會把兩者定義為同一件事情。
 
## React 測試
Facebook has built [Test Utilities](https://facebook.github.io/react/docs/test-utils.html) into React.，但由於不夠好用，所以目前主流開發社群比較傾向使用 Airbnb 團隊開發的 [enzyme](https://github.com/airbnb/enzyme)，其可以與市面上常見的測試工具（[Mocha](https://mochajs.org/)、[Karma](https://karma-runner.github.io/)、Jest 等）搭配使用。其中 [Jest](https://facebook.github.io/jest/) 是 Facebook 所開發的單元測試工具，其主要基於 [Jasmine](http://jasmine.github.io/) 所建立的測試框架。Jest 除了支援 JSDOM 外，也可以自動模擬 (mock) 透過 `require()` 進來的模組，讓開發者可以更專注在目前被測試的模組中。

## React Native
[React Native](https://facebook.github.io/react-native/)和過去的 [Apache Cordova](https://cordova.apache.org/) 等基於 WebView 的解決方案比較不同，它讓開發者可以使用 React 和 JavaScript 開發原生應用程式（Native App），讓 `Learn once, write anywhere` 理想變得可能。

## GraphQL/Relay
[GraphQL](http://graphql.org/docs/getting-started/), developed by Facebook, is a Data Query Language，trying to solve problems related to RESTful AP and allowing for more flexible API designing. [Relay](https://facebook.github.io/relay/), also developed by Facebook, is a declarative data framework, which works together with GraphQL. It brings down the number of Ajax calls, similar to Netflix's [Falcor](https://netflix.github.io/falcor/)）. Given the maintream backend API are still RESTful API,  adopting GraphQL would require architectural changes. We relegate GraphQL/Relay to appendices，for reference for those who are interested.

## Summary
We have explored all the major comonents in the React ecosystem. The task of learning React seems daunting, but not to worry - as the Chinese saysing goes, we will look for a steed with the aid of its picture. We will go over all the major components of the React ecosystem and guide your step by step towards writing useful applications.

## Further Reading
1. [Navigating the React.JS Ecosystem](https://www.toptal.com/react/navigating-the-react-ecosystem)
2. [petehunt/react-howto](https://github.com/petehunt/react-howto#learning-relay-falcor-etc)
3. [React Ecosystem - A summary](https://staminaloops.github.io/undefinedisnotafunction/react-ecosystem/)
4. [React Official Site](https://facebook.github.io/react/)
5. [A collection of awesome things regarding React ecosystem](https://github.com/enaqx/awesome-react)
6. [Webpack Handbook (Chinese)](http://zhaoda.net/webpack-handbook/index.html)
7. [Difference between AMD 和 CMD (Chinese)](https://www.zhihu.com/question/20351507)
8. [jslint to eslint](https://www.qianduan.net/jslint-to-eslint/)
9. [Facebook的Web开发三板斧：React.js、Relay和GraphQL](http://1ke.co/course/595)
10. [airbnb/javascript](https://github.com/airbnb/javascript)

（image via [jpsierens](http://jpsierens.com/wp-content/uploads/2016/06/react-eco-wp.gif)）

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: Introduction to Web Frontend Engineering](https://github.com/druckenclam/reactjs101/blob/en/Ch01/front-end-introduction.md) | [Next Chapter: Developent Environment Setup & Webpack Basics](https://github.com/druckenclam/reactjs101/blob/en/Ch02/webpack-dev-enviroment.md) |

| [Correction, Questions, and Wish List](https://github.com/kdchang/reactjs101/issues) |
