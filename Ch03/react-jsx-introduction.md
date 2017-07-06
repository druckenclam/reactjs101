# Introduction to JSX

![JSX 簡明入門教學指南](./images/reactjs.png)

## Introduction
According to [React](https://facebook.github.io/react/) official website, React ia a JavaScritp Library for building user interface. In terms of MVC, ReactJS is mainly responsible for view. For a very long time, We have been preached the virtues of separation of HTML, CSS , JavaScript. They don't mix. However，from React's perspective, everything is based on Component. One should putting everything related to a Component together. We also use [JSX](https://facebook.github.io/jsx/) to write components. In fact, JSX is not a new language, but [Syntatic Sugar](https://en.wikipedia.org/wiki/Syntactic_sugar)）, similar to [XML](https://zh.wikipedia.org/wiki/XML), an extension to ECMAScript. JSX tags are closely connected to programms that implements the tags. We need to think in terms of components (using ES6).

JSX utilize the powerful features of JavaScript, abandoning templates. This is different from [Angular](https://angularjs.org/)'s notion for enhancing HTML. JSX is not mandatory; you can use React without JSX. Eventually JSX will turn into JavaScript (only thing that web browser understands). After reading the material below, you shall be able to understand JSX and why it's a good choice.

## 一、Benefits of using JSX

### 1. Provide Semantic Tags
Due to its similarity to XML, JSX makes it easier for non-developers to understand and modify. If we want to write a form in HTML, it will look like:

```html
<form class="messageBox">
  <textarea></textarea>
  <button type="submit"></button>
</form>
```

JSX, similar to XML, allows for definition of custom tags with openning tags and closing ones.

```js
<MessageBox />
```

React believes component are better than templat and display logic in terms of separation of concerns. JSX helps implement `Declarative`, not `Imperative` UI designing:

![Facebook 上面按讚功能](./images/fb_like.jpg)

Taking above like functionality of Facebook for example, `Imperative` code will look like:

```js

if(userLikes()) {
  if(!hasBlueLike()) {
    removeGrayLike();
    addBlueLike();
  }
} else {
  if(hasBlueLike()) {
    removeBlueLike();
    addGrayLike();
  }
}

```

`Declarative` will be like:

```js
if(this.state.liked) {
  return (<BlueLike />);
} else {
  return (<GrayLike />);
}
```

Do you feel that `React` + `JSX` are easier to read? In fact, as components grow more complex, JSX makes it more intuitive, more readable.

### 2. More Concise
JSX will be turned into JavaScript, but it makes programming simpler. Below are comparison between using JSX and not using JSX.

```html
<a href="https://facebook.github.io/react/">Hello!</a>
```

Not using JSX (remember JSX is optional):

```js
// React.createElement(Components/HTML tag, Properties, Objects, Sub Components)
React.createElement('a', {href: 'https://facebook.github.io/react/'}, 'Hello!')
```

### 3. Combining JavaScript Native Syntax
JSX is not a new language, but (Syntatic Sugar), an XML like extension to ECMAScript. It doesn't change JavaScript semantics, fully realizing JavaScript's potentials. Below is an example using `map` methods, calculating `result` iteratively. It generates (ul) without using templates (`<li>` requires a key to be added map function, we use index for now. Without a key, reconciliation will be affected):

```js
// const indicates it's constant
const lists = ['JavaScript', 'Java', 'Node', 'Python'];

class HelloMessage extends React.Component {
  render() {
    return (
    <ul>
      {lists.map((result, index) => {
        return (<li key={index}>{result}</li>);
      })}
    </ul>);
  }
}
```

## 二、JSX Usage
### 1. Environment Setup
Now that we know whey we need JSX, we are moving on to how ot use it. There are two ways to use it:

1. Use [browserify](http://browserify.org/) or  [webpack](https://webpack.github.io/) [CommonJS](https://en.wikipedia.org/wiki/CommonJS) bundler + [babel](https://babeljs.io/) to preprocess JSX
2. Preprocess in browser

For the sake of simplicity, we use 2nd one first, focusing use on JSX syntax. In later chapters, we will cover how to use bundler (one can try to paste code below to [JSbin](http://jsbin.com/)):

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
    <!-- Introduce, in index.html, react.js, react-dom.js, and browser.min.js of babel-core -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/15.0.1/react.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/15.0.1/react-dom.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-core/5.8.23/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      // Code starts here
      ReactDOM.render(
        <h1>Hello, world!</h1>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

Load JSX：

- embedded

```html
<script type="text/babel">
  ReactDOM.render(
    <h1>Hello, world!</h1>,
    document.getElementById('example')
  );
</script>
```

- from a file

`<script type="text/jsx" src="main.jsx"></script>` 


### 2. Tag Usages
JSX tags are very similar to XML. Component's first letter should be capitalized;  HTML Tags use all small letters. Below is a class based Component:

```js
class HelloMessage extends React.Component {
  render() {
    return (
      <div>
        <p>Hello React!</p>
        <MessageList />
      </div>
    );
  }
}
```

### 3. Convertion to JavaScript 

JSX will be transpiled to JavaScript:

```js
React.createElement(
  string/ReactClass, // React Component or HTML
  [object props], // attributes (using objects)
  [children] // sub components
)
```

Before transpilation（remember enclose JavaScript Code with `{}`. For example,  `text` is a variable. To use string literal, one need to use `''`）：

```js
var text = 'Hello React';
<h1>{text}</h1>
<h1>{'text'}</h1>
```

After transpilation:

```js
var text = 'Hello React';
React.createElement("h1", null, "Hello React!");
```

Because every JSX tag correspond to one JavaScript function, so the `render` function of Components can only return one Root Node. If you want to return multiple tags, they need to be enclosed by a component or, a `<div>`, or `<span>`.

### 4. Comments
JSX Will be transpiled into JavaScript. It uses `//` 和 `/**/` for comments.

```js
// line comments

/*
  multiple line comments
*/

var content = (
  <List>
      {/* enclosed by {} within JSX */}
      <Item
        /* multiple
           line
           comments */
        name={window.isLoggedIn ? window.name : ''} // line comment
      />
  </List>
);
```

### 5. Attributes
HTML allows us to change attributes to change style of tags, so is true for JSX. Due to the fact that `class` and `for` are JavaScript reserved words, they are replaced by `className` and `htmlFor` in JSX respectively.

```js
class HelloMessage extends React.Component {
  render() {
    return (
      <div className="message">
        <p>Hello React!</p>
      </div>
    );
  }
}
```

#### Boolean Attributes
In JSX boolean attributes with values omitted default to `true`. For example, the first input tag below has a `disabled` attribute without value, which has the same effect as the second input tag:

```html
<input type="button" disabled />;
<input type="button" disabled={true} />;
```

If an attributes doesn't appear, it's deemed `false`:

```html
<input type="button" />;
<input type="button" disabled={false} />;
```

### 6. Spread Syntax
ES6 use `...` expand iterable items, a attribute after attributes of the same name will take precedence.

```js
var props = {
  style: "width:20px",
  className: "main",
  value: "yo",  
}

<HelloMessage  {...props} value="yo" />

// After transpilation
React.createElement("h1", React._spread({}, props, {value: "yo"}), "Hello React!");

```

### 7. Self-defined Attribute
Self-defined attributes starts with `data-`：

```js
<HelloMessage data-attr="xd" />
```

### 8. Display HTML
For security, by default, the HTML tags are sanitized. In order to show them, one can use _html:

```html
<div>{{_html: '<h1>Hello World!!</h1>'}}</div>
```

### 9. CSS
You can use either JSX with one `{}`, or JavaScript objects. It uses camelCase:

```js
<HelloMessage style={{ color: '#FFFFFF', fontSize: '30px'}} />
```

### 10. Event Handling
Event handling is the crux of the issue facing frontend developers. JSX use inline event binding (also camelCase). Please refere to [Official Reference] (https://facebook.github.io/react/docs/events.html#supported-events) for details.

```js
<HelloMessage onClick={this.onBtn} />
```

## Summary
Above is our introductary tutorial to JSX. Hopefully readers now understands why React uses JSX, and the basic concepts and usages of JSX. A brief recap: React is Component based. A components combines resources together including JavaScript, Html, and CSS. React Components can be better written with [JSX] (https://facebook.github.io/jsx/), which resembles XML, serving as an extension to ECMAScript. It aims to replace templates. It should be noted, JSX is not mandatory, as it eventually will be transpiled into JavaScript. We believe you should consider seriously about using JSX if you haven't been doing so.

## Further Reading
1. [Imperative programming or declarative programming](http://www.puritys.me/docs-blog/article-320-Imperative-programming-or-declarative-programming.html)
2. [JSX in Depth](https://facebook.github.io/react/docs/jsx-in-depth.html)
3. [Learn React from Scratch (ReactJS 101) (Chinese)](https://www.gitbook.com/book/kdchang/react101/details)

（image via [adweek](http://www.adweek.com/socialtimes/files/2014/05/LikeButtoniOSApps650.jpg), [codecondo](http://codecondo.com/wp-content/uploads/2015/12/Useful-Features-of-React_7851.png)）

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: Introduction to React/JSX/Component](https://github.com/druckenclam/reactjs101/blob/en/Ch03/reactjs-introduction.md) | [Next Chapter: Props、State、Refs and form processing](https://github.com/druckenclam/reactjs101/blob/en/Ch04/props-state-introduction.md) |

| [Correction, Questions, and Wish List](https://github.com/kdchang/reactjs101/issues) |
