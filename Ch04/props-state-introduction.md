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
// TodoApp consists of TodoList Components, which show TODOs. Todos are passed down via props into TodoList. TodoList only Renders UI, doesn't change state, so it is a stateless component, and we use Functional Component. Please note we use map function to iterate Todos. Each li must have a unique key, or there will be errors (one can define one's own id, or use the 2nd argument of map function -- index)
const TodoList = (props) => (
	<ul>
		{
			props.items.map((item) => (
				<li key={item.id}>{item.text}</li>
			))
		}
	</ul>
)

// App component shows event handling
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

The above covers React event handling. Other than `onChange` and `onSubmit`, React also encapsulate other common events, such as `onClick`. For more events, one can refer to [Official Website Event System](https://facebook.github.io/react/docs/events.html)。

## Refs and Form Processing
We have covered props (immutable), state (Changes with interaction with users), and event handling. We will introduce how to process form in submit. We will use official example A Component Using External Plugins for example. It's very easy to integrate React with 3rd party libraries such as JQuery. We are using `remarkable` + `ref` to retrieve DOM Value. (Alternatively, one can use `onChange`). User can use Markdown editor.

HTML Markup (use CDN to introduce `react`, `react-dom` and `remarkable`, the `Markdown` parser. If Webpack or browserify + babelify is not used, one need to add type="text/babel" to script if ES6 is used):

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
	// Parse Markdown into HTML and store the resultant HTML into DOM. React uses virtual DOM to communicate with DOM. Direct operations on DOM is not recommended. So the attribute is set to dangerouslySetInnerHTML
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
We have used examples from React Official Website to demonstrate Props, State, and React event handling, and form submission. If you are not familar with those material, it's recommended to type in the code and run it yourself. Or you can practice with tools like [jsbin](http://jsbin.com/)!Next we will cover Component Life-Cycle.

## Further Reading
1. [React Official Site](https://facebook.github.io/react/index.html)
2. [Top-Level API](https://facebook.github.io/react/docs/top-level-api.html)
3. [Javascript：this usage (Chinese)](https://software.intel.com/zh-cn/blogs/2013/10/09/javascript-this)

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: Introduction to JSX](https://github.com/druckenclam/reactjs101/blob/en/Ch03/react-jsx-introduction.md) | [Next Chapter：React Component Life Cycle](https://github.com/druckenclam/reactjs101/blob/en/Ch04/react-component-life-cycle.md) |

| [Correction, Questions, and Wish List](https://github.com/kdchang/reactjs101/issues) |
