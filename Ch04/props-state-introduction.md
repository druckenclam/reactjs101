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
// Functional Component 可以視為 f(d) => UI，根據傳進去的 props 繪出對應的 UI。注意這邊 props 是傳入函式的參數，因此取用 props 不用加 this
const HelloMessage = (props) => (
	<div>Hello {props.name}</div>
);

// PropTypes verification, if props type is not string, an error will be flagged
HelloMessage.propTypes = {
  name: React.PropTypes.string,
}

// Prop default value. If props doesn't have name attribute, the default value will be Zuck. It's equivalent to getDefaultProps in ES5.
HelloMessage.defaultProps = {
 name: 'Zuck',
}

ReactDOM.render(<HelloMessage name="Mark" />, document.getElementById('app'));
```

Example on jsbin:

<a class="jsbin-embed" href="http://jsbin.com/wadice/embed?html,js,console,output">A Component Using External Plugins on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.39.12"></script>

## State
接下來我們將使用 A Stateful Component 這個範例來講解 State 的用法。在 React Component 可以自己管理自己的內部 state，並用 `this.state` 來存取 state。當 `setState()` 方法更新了 state 後將重新呼叫 `render()` 方法，重新繪製 component 內容。以下範例是一個每 1000 毫秒（等於1秒）就會加一的累加器。由於這個範例是 Stateful Component 因此僅使用 ES6 Class Component，而不使用 Functional Component。

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
		// 與 ES5 React.createClass({}) 不同的是 component 內自定義的方法需要自行綁定 this context，或是使用 arrow function
        this.tick = this.tick.bind(this);
		// 初始 state，等於 ES5 中的 getInitialState
		this.state = {
			secondsElapsed: 0,
		}
	}
	// 累加器方法，每一秒被呼叫後就會使用 setState() 更新內部 state，讓 Component 重新 render
	tick() {
	    this.setState({secondsElapsed: this.state.secondsElapsed + 1});
	}
	// componentDidMount 為 component 生命週期中階段 component 已插入節點的階段，通常一些非同步操作都會放置在這個階段。這便是每1秒鐘會去呼叫 tick 方法
	componentDidMount() {
	    this.interval = setInterval(this.tick, 1000);
	}
	// componentWillUnmount 為 component 生命週期中 component 即將移出插入的節點的階段。這邊移除了 setInterval 效力
	componentWillUnmount() {
		clearInterval(this.interval);
	}
	// render 為 class Component 中唯一需要定義的方法，其回傳 component 欲顯示的內容
	render() {
	    return (
	      <div>Seconds Elapsed: {this.state.secondsElapsed}</div>
	    );
	}
}

ReactDOM.render(<Timer />, document.getElementById('app'));
```

關於 Javascript this 用法可以參考 [Javascript：this用法整理](https://software.intel.com/zh-cn/blogs/2013/10/09/javascript-this)。

## 事件處理（Event Handle）
在前面的內容我們已經學會如何使用 props 和 state，接下來我們要更進一步學習在 React 內如何進行事件處理。下列將使用 React 官網的 An Application 當做例子，實作出一個簡單的 TodoApp。

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
以上透過幾個 React 官網首頁上的範例介紹了 Props 和 State 特性及在 React 如何進行事件和表單處理這些 React 中核心的問題，若還不熟悉的讀者建議重新親自動手照著範例中的程式碼敲過一遍，也可以使用像 [jsbin](http://jsbin.com/) 這樣所見即所得的工具來練習，更能熟悉相關語法和 API 喔！接下來我們將探討 Component 的生命週期。

## Further Reading
1. [React Official Site](https://facebook.github.io/react/index.html)
2. [Top-Level API](https://facebook.github.io/react/docs/top-level-api.html)
3. [Javascript：this usage (Chinese)](https://software.intel.com/zh-cn/blogs/2013/10/09/javascript-this)

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: Introduction to JSX](https://github.com/druckenclam/reactjs101/blob/en/Ch03/react-jsx-introduction.md) | [Next Chapter：React Component 規格與生命週期（Life Cycle）](https://github.com/druckenclam/reactjs101/blob/en/Ch04/react-component-life-cycle.md) |

| [Correction, Questions, and Wish List](https://github.com/kdchang/reactjs101/issues) |
