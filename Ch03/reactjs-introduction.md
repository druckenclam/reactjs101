# Introduction to React/JSX/Component

## Introduction
In the previous chapter, we have learned briefly how to set up React deveopment envirionment and Webpack basics. Next we will learn more about React, especially Component designing.

## ReactJS Features
React was a internal tool used by Facebook, aiming to achieve `Learn once, write anywhere`. Since entering into open source projects in 2013, the ecosystem has been booming. ReactJS revolutionized frontend engineering; Below are some revolutionary ideas:

1. Component Based Development
2. Declarative UI Design using JSX
3. Virtual DOM
4. Component PropType Error Prevention (such as type check)
5. Component is a State Machine with Life Cycle
6. Always Redraw and Unidirectional Data Flow
7. Use CSS：Inline Style in Javascript 

## Component Based Development

![ReactJS 與 Component 設計入門介紹](./images/component.png "ReactJS 與 Component 設計入門介紹")

The basic building blocks for React are Components. A component, made up of other components, is a Composable components. This allows for encapsulation, Separation of Concerns, Reuse, and composition.

`<TodoApp>` Component contains `<TodoHeader />`、`<TodoList />` sub components
```javascript
	<div>
		<TodoHeader />
		<TodoList />
	</div>
```

`<TodoList />` sub components
```javascript
	<div>
		<ul>
			<li>Coding</li>
			<li>Dating</li>
			<li>Book Purchase</li>
		</ul>
	</div>
```

Components based development are the the holy grail for frontend engineering. Many devlopers hope to get reusablity to a maximum level, and to not repeat yourselves (DRY). In React components are the basis of everything; development is like playing with building blocks. It's somwwhat chanllenge for frontend engineers who are more familiar with template based development. It's not easy to make the transition from templates to components, especially given that in the past we separate HTML, CSS and JavaScript and that now we want to group them together.

One way to do it is to train ourselves to think in terms of components, to break down websites into individual components. After a while, we will get used to component based way of thinking.

React Component can be written in two ways:

1. ES6 Class Components (Allows for more complex operations and control of the life cycle, but consumes more resources)

	```javascript
	//  The first letter should be captalized
	class MyComponent extends React.Component {
		// render is the only mandatory method for Class
		render() {
			return (
				<div>Hello, World!</div>
			);
		}
	}

	// Mount <MyComponent /> at a DOM element identified by id app
	ReactDOM.render(<MyComponent/>, document.getElementById('app'));
	```

2. Functional Component (pure stateless components for UI rendering, there is no status, 實作物件, reference，or life-cycle functions. Stateless components are faster than stateful ones)

	```javascript
	// Use arrow function to write Functional Component, making UI design simpler and reducing side effect
	const MyComponent = () => (
		<div>Hello, World!</div>
	);
	
	// mounting <MyComponent /> at the DOM element identified by id (app)
	ReactDOM.render(<MyComponent/>, document.getElementById('app'));
	```

## Declarative UI Design with JSX
React 在設計上的思路認為使用 Component 比起模版（Template）和顯示邏輯（Display Logic）更能實現關注點分離的概念，而搭配 JSX 可以實現聲明式 Declarative（注重 what to），而非命令式 Imperative（注重 how to）的程式撰寫方式。

像下述的宣告式（Declarative）UI 設計就比單純用（Template）式的方式更易懂：

```javascript
// 使用宣告式（Declarative）UI 設計很容易可以看出這個元件的功能
<MailForm />
```

```javascript
// <MailForm /> 內部長相
<form>
	<input type="text" name="email" />
	<button type="submit"></button>
</form>
```

由於 JSX 在 React 元件撰寫上扮演很重要的角色，因此在下一個章節我們也將更深入講解 JSX 使用細節。 

## Virtual DOM
在傳統 Web 中一般是使用 jQuery 進行 DOM 的直接操作。然而更改 DOM 往往是 Web 效能的瓶頸，因此在 React 世界設計有 Virtual DOM 的機制，讓 App 和 DOM 之間用 Virtual DOM 進行溝通。當更改 DOM 時，會透過 React 自身的 diff 演算法去計算出最小更新，進而去最小化更新真實的 DOM。

## Component PropType Error Prevention
在 React 設計時除了提供 props 預設值設定（Default Prop Values）外，也提供了 Prop 的驗證（Validation）機制，讓整個 Component 設計更加穩健：

```javascript
//  注意元件開頭第一個字母都要大寫
class MyComponent extends React.Component {
	// render 是 Class based 元件唯一必須的方法（method）
	render() {
		return (
			<div>Hello, World!</div>
		);
	}
}

// PropTypes 驗證，若傳入的 props type 不符合將會顯示錯誤
MyComponent.propTypes = {
  todo: React.PropTypes.object,
  name: React.PropTypes.string,
}

// Prop 預設值，若對應 props 沒傳入值將會使用 default 值
MyComponent.defaultProps = {
 todo: {}, 
 name: '', 
}
```

關於更多的 Validation 用法可以參考[官方網站](https://facebook.github.io/react/docs/reusable-components.html) 的說明。

## Component Operates like a State Machine with Life Cycle
Component 就像個狀態機（State Machine），根據不同的 state（透過 `setState()` 修改）和 props（由父元素傳入），Component 會出現對應的顯示結果。而人有生老病死，元件也有生命週期。透過操作生命週期處理函數，可以在對應的時間點進行 Component 需要的處理，關於更詳細的元件生命週期介紹我們會再下一個章節進行更一步說明。

## Always Redraw and Unidirectional Data Flow
在 React 世界中，props 和 state 是影響 React Component 長相的重要要素。其中 props 都是由父元素所傳進來，不能更改，若要更改 props 則必須由父元素進行更改。而 state 則是根據使用者互動而產生的不同狀態，主要是透過 setState() 方法進行修改。當 React 發現 props 或是 state 更新時，就會重繪整個 UI。當然你也可以使用 forceUpdate() 去強迫重繪 Component。而 React 透過整合 Flux 或 Flux-like（例如：Redux）可以更具體實現單向資料流（Unidirectional Data Flow），讓資料流的管理更為清晰。

## 在 JavaScript 裡寫 CSS：Inline Style 
在 React Component 中 CSS 使用 Inline Style 寫法，全都封裝在 JavaScript 當中：

```javascript
const divStyle = {
  color: 'red',
  backgroundImage: 'url(' + imgUrl + ')',
};

ReactDOM.render(<div style={divStyle}>Hello World!</div>, document.getElementById('app'));
```

## Summary
We have covered some of the most important ReactJS features:

1. Component Based Development
2. Declarative UI using JSX
3. Virtual DOM
4. Component PropType Error Prevention
5. Component operates like a State Machine with Life Cycle
6. Always Redraw and Unidirectional Data Flow
7. CSS：Inline Style in Javascript

We will learn more about how to use JSX with React.

## Further Reading
1. [React Tutorial (chinese)](http://www.ruanyifeng.com/blog/2015/03/react.html)
2. [React Demystified](http://blog.reverberate.org/2014/02/react-demystified.html)
3. [Top-Level API](https://facebook.github.io/react/docs/top-level-api.html)
4. [ES6 Classes Component](https://facebook.github.io/react/docs/reusable-components.html#es6-classes)

（image via [maketea](http://maketea.co.uk/images/2014-03-05-robust-web-apps-with-react-part-1/wireframe_deconstructed.png)）

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: Developent Environment Setup & Webpack Basics](https://github.com/druckenclam/reactjs101/blob/en/Ch02/webpack-dev-enviroment.md) | [Next Chapter: JSX Introductary Tutorial](https://github.com/druckenclam/reactjs101/blob/en/Ch03/react-jsx-introduction.md) |

| [Correction, Questions, and Wish List](https://github.com/kdchang/reactjs101/issues) |
