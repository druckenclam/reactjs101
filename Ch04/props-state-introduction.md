# Next Chapter: Props、State、Refs and form processing

## Introduction
We have established the basic notion of React and JSX. In essence, React Component is a state machine, representing UI. The state (changed by `setState()`) and props (from parents). Component will display the logic based on states. This chapter will use [React Official Example](https://facebook.github.io/react/index.html) (with ES6+) to demonstrate Props and State. It also covers how React handles event and forms.

## Props
First we use an example from React official website to demonstrate the usage of props. The attribute name is Mark, so the program below will display Hello, Mark in browsers. React has verification scheme for props, making our components more robust.

HTML Markup：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>A Component Using External Plugins</title>
</head>
<body>
<!-- Use CDN to import react and react-dom. In real projects, we will use webpack -->
<script src="https://fb.me/react-15.1.0.js"></script>
<script src="https://fb.me/react-dom-15.1.0.js"></script>
  <div id="app"></div>
	<script src="./app.js"></script>
</body>
</html>
```

app.js, use ES6 Class Component Syntax:

```javascript
class HelloMessage extends React.Component {
	// If you want to bind this or use state, constructor is required. 
	constructor(props) {
		// Readers with OOP background should be familiar with constructors. It's only syntax sugar; under the cover, it is still prototype based. extends is used for inheritance. super use to call constructor of parents
		super(props);
		this.state = {}
	}
	// render is the only mandatory method. If it's a pure UI, it's recommended to use Functional Component for simplicity and performace
	render() {
		return (
			<div>Hello {this.props.name}</div>
		)
	}
}

// PropTypes Verification, If props type is not string, an error will be flagged
HelloMessage.propTypes = {
  name: React.PropTypes.string,
}

// Prop default value, If props don't have name attribute, the default value will be Zuck
HelloMessage.defaultProps = {
 name: 'Zuck',
}

ReactDOM.render(<HelloMessage name="Mark" />, document.getElementById('app'));
```

Regarding React ES6 class constructor super() one can refer to [React ES6 class constructor super()](http://cheng.logdown.com/posts/2016/03/26/683329) 。

Use Functional Component:

```javascript
// Functional Component takes the form f(d) => UI. It draws UI based on the input props. props is an argument to a function. so no this is needed
const HelloMessage = (props) => (
	<div>Hello {props.name}</div>
);

// PropTypes verification, if props type is not string, an error will be flagged
HelloMessage.propTypes = {
  name: React.PropTypes.string,
}

// Prop default value. If props doesn't have name attribute, the default value will be Zuck. It's equivalent to getDefaultProps in React + ES5.
HelloMessage.defaultProps = {
 name: 'Zuck',
}

ReactDOM.render(<HelloMessage name="Mark" />, document.getElementById('app'));
```

Example on jsbin:

<a class="jsbin-embed" href="http://jsbin.com/wadice/embed?html,js,console,output">A Component Using External Plugins on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.39.12"></script>

## State
Below we will use a Stateful Component to illustrate State. React Component manages an internal state, referenced by `this.state`. When `setState()` is invoked to update state, `render()` will be invoked to redraw the component. Below is a counter that increase itself by 1 every 1000 ms (1s). It's a Stateful Component, so we use ES6 Class Component, rather than Functional Component.

HTML Markup：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>A Component Using External Plugins</title>
</head>
<body>
<script src="https://fb.me/react-15.1.0.js"></script>
<script src="https://fb.me/react-dom-15.1.0.js"></script>
  <div id="app"></div>
	<script src="./app.js"></script>
</body>
</html>
```

app.js：

```javascript
class Timer extends React.Component {
	constructor(props) {
		super(props);
		// Different from ES5 React.createClass({}) components built-in method need to bind this context, or use arrow function
        this.tick = this.tick.bind(this);
		// Initialize state，equivalent to ES5 getInitialState
		this.state = {
			secondsElapsed: 0,
		}
	}
	// counter, call setState() every second to update state, forcing invocation of render
	tick() {
	    this.setState({secondsElapsed: this.state.secondsElapsed + 1});
	}
	// componentDidMount is a hook when component is mounted. Usually asynchronuous operations happen here. The code below calls tick very second.
	componentDidMount() {
	    this.interval = setInterval(this.tick, 1000);
	}
	// componentWillUnmount happens before component is unmounted. Below we remove the timer.
	componentWillUnmount() {
		clearInterval(this.interval);
	}
	// render is the only mandatory method, which returns the content for component display.
	render() {
	    return (
	      <div>Seconds Elapsed: {this.state.secondsElapsed}</div>
	    );
	}
}

ReactDOM.render(<Timer />, document.getElementById('app'));
```

Javascript this usages can be found at [Javascript：this usage (Chinese)](https://software.intel.com/zh-cn/blogs/2013/10/09/javascript-this)。

## Event Handle
We have covered the basics of props and state. Next up we will learn how to handle events in React. We will use React official example An Application, make a simple TodoApp.

HTML Markup：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>A Component Using External Plugins</title>
</head>
<body>
<script src="https://fb.me/react-15.1.0.js"></script>
<script src="https://fb.me/react-dom-15.1.0.js"></script>
  <div id="app"></div>
	<script src="./app.js"></script>
</body>
</html>
```

app.js：

```javascript
// TodoApp 元件中包含了顯示 Todo 的 TodoList 元件，Todo 的內容透過 props 傳入 TodoList 中。由於 TodoList 僅單純 Render UI 不涉及內部 state 操作是 stateless component，所以使用 Functional Component 寫法。需要特別注意的是這邊我們用 map function 來迭代 Todos，需要留意的是每個迭代的元素必須要有 unique key 不然會發生錯誤（可以用自定義 id，或是使用 map function 的第二個參數 index）
const TodoList = (props) => (
	<ul>
		{
			props.items.map((item) => (
				<li key={item.id}>{item.text}</li>
			))
		}
	</ul>
)

// 整個 App 的主要元件，這邊比較重要的是事件處理的部份，內部有
class TodoApp extends React.Component {
	constructor(props) {
		super(props);
		this.onChange = this.onChange.bind(this);
		this.handleSubmit = this.handleSubmit.bind(this);
		this.state = {
			items: [],
			text: '',
		}
	}
	onChange(e) {
    	this.setState({text: e.target.value});
	}
	handleSubmit(e) {
    	e.preventDefault();
    	const nextItems = this.state.items.concat([{text: this.state.text, id: Date.now()}]);
    	const nextText = '';
    	this.setState({items: nextItems, text: nextText});
	}
	render() {
	    return (
	      <div>
	        <h3>TODO</h3>
	        <TodoList items={this.state.items} />
	        <form onSubmit={this.handleSubmit}>
	          <input onChange={this.onChange} value={this.state.text} />
	          <button>{'Add #' + (this.state.items.length + 1)}</button>
	        </form>
	      </div>
	    );
	}
}

ReactDOM.render(<TodoApp />, document.getElementById('app'));
```

以上介紹了 React 事件處理的部份，除了 `onChange` 和 `onSubmit` 外，React 也封裝了常用的事件處理，如 `onClick` 等。若想更進一步了解有哪些可以使用的事件處理方法可以參考 [官網的 Event System](https://facebook.github.io/react/docs/events.html)。

## Refs 與表單處理
上面介紹了 props（傳入後就不能修改）、state（隨著使用者互動而改變）和事件處理機制後，我們將接續介紹如何在 React 中進行表單處理。同樣我們使用 React 官網範例 A Component Using External Plugins 進行介紹。由於 React 可以容易整合外部的 libraries（例如：jQuery），本範例將使用 `remarkable` 結合 `ref` 屬性取出 DOM Value 值（另外比較常用的作法是使用 `onChange` 事件處理方式處理表單內容），讓使用者可以使用 Markdown 語法的所見即所得編輯器（editor）。

HTML Markup（除了引入 `react` 、 `react-dom` 還要用 `CDN` 方式引入 `remarkable` 這個 `Markdown` 語法 parser 套件，記得如果沒有使用 Webpack 或是 browserify + babelify 等工具需要引入 `babel-standalone` 瀏覽器解析 ES6 語法並於引入 script 加上 type="text/babel"）：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>A Component Using External Plugins</title>
</head>
<body>
<script src="https://fb.me/react-15.1.0.js"></script>
<script src="https://fb.me/react-dom-15.1.0.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.18.1/babel.min.js"></script>
<script src="https://cdn.jsdelivr.net/remarkable/1.6.2/remarkable.min.js"></script>
  <div id="app"></div>
	<script type="text/babel" src="./app.js"></script>
</body>
</html>
```

app.js：

```javascript
class MarkdownEditor extends React.Component {
	constructor(props) {
		super(props);
		this.handleChange = this.handleChange.bind(this);
		this.rawMarkup = this.rawMarkup.bind(this);
		this.state = {
			value: 'Type some *markdown* here!',
		}
	}
	handleChange() {
	    this.setState({value: this.refs.textarea.value});
	}
	// 將使用者輸入的 Markdown 語法 parse 成 HTML 放入 DOM 中，React 通常使用 virtual DOM 作為和 DOM 溝通的中介，不建議直接由操作 DOM。故使用時的屬性為 dangerouslySetInnerHTML
	rawMarkup() {
	    const md = new Remarkable();
	    return { __html: md.render(this.state.value) };
	}
	render() {
	    return (
	      <div className="MarkdownEditor">
	        <h3>Input</h3>
	        <textarea
	          onChange={this.handleChange}
	          ref="textarea"
	          defaultValue={this.state.value} />
	        <h3>Output</h3>
	        <div
	          className="content"
	          dangerouslySetInnerHTML={this.rawMarkup()}
	        />
	      </div>
	    );
	}
}

ReactDOM.render(<MarkdownEditor />, document.getElementById('app'));
```

## Summary
以上透過幾個 React 官網首頁上的範例介紹了 Props 和 State 特性及在 React 如何進行事件和表單處理這些 React 中核心的問題，若還不熟悉的讀者建議重新親自動手照著範例中的程式碼敲過一遍，也可以使用像 [jsbin](http://jsbin.com/) 這樣所見即所得的工具來練習，更能熟悉相關語法和 API 喔！Next we will cover Component Life-Cycle.

## Further Reading
1. [React Official Site](https://facebook.github.io/react/index.html)
2. [Top-Level API](https://facebook.github.io/react/docs/top-level-api.html)
3. [Javascript：this usage (Chinese)](https://software.intel.com/zh-cn/blogs/2013/10/09/javascript-this)

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: Introduction to JSX](https://github.com/druckenclam/reactjs101/blob/en/Ch03/react-jsx-introduction.md) | [Next Chapter：React Component 規格與生命週期（Life Cycle）](https://github.com/druckenclam/reactjs101/blob/en/Ch04/react-component-life-cycle.md) |

| [Correction, Questions, and Wish List](https://github.com/kdchang/reactjs101/issues) |
