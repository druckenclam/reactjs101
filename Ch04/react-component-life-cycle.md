# React Component Life Cycle

## Introduction
We have learned a bit how to develop React components. Now we will delve deeper into React Component specification and life cycle.

## React Component Specification
We have introduced two ways of writing React components: one is use ES6 Class; the other Stateless Components -- Functional Component to display UI only. Below is a recap of the last chapter's example:

1. Use ES6 Class for complex operation and life-cycle control

	```javascript
	//  The first letter must be capitalized
	class MyComponent extends React.Component {
		// render is the only mandatory method for Class based method
		render() {
			return (
				<div>Hello, {this.props.name}</div>
			);
		}
	}

	// PropTypes verification, if props type doesn't conform, it will show error
	MyComponent.propTypes = {
		name: React.PropTypes.string,
	}

	// Prop default value. 
	MyComponent.defaultProps = {
	 	name: '',
	}

	// Mount <MyComponent /> to element marked by id app
	ReactDOM.render(<MyComponent name="Mark"/>, document.getElmentById('app'));
	```

2. Use Functional Component (for pure UI render, stateless components, no internal state and no ref, no life cycle functions.)

	```javascript
	// Use arrow function to simplify Functional Components and reduce side effect
	const MyComponent = (props) => (
		<div>Hello, {props.name}</div>
	);

	// PropTypes verification. If the constraints are not met, errors will be flagged.
	MyComponent.propTypes = {
		name: React.PropTypes.string,
	}

	// Prop default value. If no value is passed in, the default value will be used.
	MyComponent.defaultProps = {
		name: '',
	}

	// The mounting point for react component.
	ReactDOM.render(<MyComponent name="Mark"/>, document.getElmentById('app'));
	```

In ES6 Class has only one mandatory method `render()`. (Please don't change or use asynchronous method talking to browsers. Any asynchronous communication must placed in `componentDidMount()`). Functional Component allows for `return null`. ES6 no longer support `mixins` or other methods of component reuse.

## React Component Cycle Life
React Components, like people, have life cycles. There are three status for a Component's life-cycle:

1. Mounting：Inserting to DOM
2. Updating：Rerendering
3. Unmounting：Unmounted from DOM

React Provides Hooks below to perform actions according to life-cycles:

1. Mounting
	- componentWillMount()
	- componentDidMount()
2. Updating
	- componentWillReceiveProps(object nextProps): Invoked when a component is about to receive new props
	- shouldComponentUpdate(object nextProps, object nextState): check if a component needs rerendering. forceUpdate() will bypass the check
	- componentWillUpdate(object nextProps, object nextState)
	- componentDidUpdate(object prevProps, object prevState)
3. Unmounting
	- componentWillUnmount()

Many readers may find Component Life Cycle abstract. We will be demonstrate it using an example below. 讀者可以發現當一開始載入元件時第一個會觸發 `console.log('constructor');`，依序執行 `componentWillMount`、`componentDidMount` ，而當點擊文字觸發 `handleClick()` 更新 `state` 時則會依序執行 `componentWillUpdate`、`componentDidUpdate`：

HTML Markup：
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <script src="https://fb.me/react-15.1.0.js"></script>
  <script src="https://fb.me/react-dom-15.1.0.js"></script>
  <title>Component LifeCycle</title>
</head>
<body>
  <div id="app"></div>
</body>
</html>
```

Component Life Cycle Demonstration

```javascript
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    console.log('constructor');
    this.handleClick = this.handleClick.bind(this);
    this.state = {
      name: 'Mark',
    }
  }
  handleClick() {
    this.setState({'name': 'Zuck'});
  }
  componentWillMount() {
    console.log('componentWillMount');
  }
  componentDidMount() {
    console.log('componentDidMount');
  }
  componentWillReceiveProps() {
    console.log('componentWillReceiveProps');
  }
  componentWillUpdate() {
    console.log('componentWillUpdate');
  }
  componentDidUpdate() {
    console.log('componentDidUpdate');
  }
  componentWillUnmount() {
    console.log('componentWillUnmount');
  }
  render() {
    return (
      <div onClick={this.handleClick}>Hi, {this.state.name}</div>
    );
  }
}

ReactDOM.render(<MyComponent />, document.getElementById('app'));
```

<a class="jsbin-embed" href="http://jsbin.com/yokebo/embed?html,js,console,output">點擊看詳細範例</a><script src="http://static.jsbin.com/js/embed.min.js?3.39.12"></script>

![React Component 規格與生命週期](./images/react-lifecycle.png)

其中特殊處理的函數 `shouldComponentUpdate`，目前預設 `return true`。若你想要優化效能可以自己編寫判斷方式，若採用 `immutable` 可以使用 `nextProps === this.props` 比對是否有變動：

```javascript
shouldComponentUpdate(nextProps, nextState) {
  return nextProps.id !== this.props.id;
}
```

## Ajax Asynchronous Processing
Ajax Asynchronous Processing is done in `componentDidMount`. The example below use `jQuery` to execute `Ajax` code to retrieve `Github API`.

HTML Markup：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <script src="https://fb.me/react-15.1.0.js"></script>
  <script src="https://fb.me/react-dom-15.1.0.js"></script>
  <script src="https://code.jquery.com/jquery-3.1.0.js"></script>
  <title>GitHub User</title>
</head>
<body>
  <div id="app"></div>
</body>
</html>
```

app.js

```javascript
class UserGithub extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
          username: '',
          githubtUrl: '',
          avatarUrl: '',
        }
    }
    componentDidMount() {
        $.get(this.props.source, (result) => {
            console.log(result);
            const data = result;
            if (data) {
              this.setState({
                    username: data.name,
                    githubtUrl: data.html_url,
                    avatarUrl: data.avatar_url
              });
            }
        });
    }
    render() {
        return (
          <div>
            <h3>{this.state.username}</h3>
            <img src={this.state.avatarUrl} />
            <a href={this.state.githubtUrl}>Github Link</a>.
          </div>
        );
    }
}

ReactDOM.render(
  <UserGithub source="https://api.github.com/users/torvalds" />,
  document.getElementById('app')
);
```

<a class="jsbin-embed" href="http://jsbin.com/kupusa/embed?html,js,output">Click on this one to see a detailed example</a><script src="http://static.jsbin.com/js/embed.min.js?3.39.12"></script>

## Summary
We have introduced React Component Specification and Life Cycle. Life Cycle is an abstract concept especially for beginners. We suggest readers to try typeing and running the examples. Next up, we will introduce `React Router`, upon which single page application can be built.

## Further Reading
1. [Component Specs and Lifecycle](https://facebook.github.io/react/docs/component-specs.html#lifecycle-methods)

（image via [react-lifecycle](http://imgh.us/react-lifecycle.svg)）

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: Props, State, Refs and Form Processing](https://github.com/druckenclam/reactjs101/blob/en/Ch04/props-state-introduction.md) | [Next Chapter: React Router Basics & Tutorial](https://github.com/druckenclam/reactjs101/blob/en/Ch05/react-router-introduction.md) |

| [Correction, Question, and Wish List](https://github.com/kdchang/reactjs101/issues) |
