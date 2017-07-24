# Flux Basics and Practical Tutorial

![React Flux](./images/react-flux.jpeg "React Flux")

## Introduction
With React App growing in complexity，we often need to use parent component's props to change child components' state tree, which is not easy to manage. We need better architecture to build complex applications. [Flux] (https://facebook.github.io/flux/), developed by Facebook, is a client-side application architecture, aiming to solve problems related to `MVC`. In fact, Flux is not a complete frontend Framework; rather it features unidirectional Data Flow, making it easier to manage state in large applications. React, responsible for view, together with Flux-like framework, states can be better managed, including those involveing interactivities, such as Facebook likes, clicking on photos, new messages.

Original Flux framework has room for improvements. We often use community Flux-like frameworks such as [Redux](http://redux.js.org/index.html), [Alt](http://alt.js.org/), and [Reflux](https://github.com/reflux/refluxjs). We mainly use Facebook `Dispatcher API` framework (can be thought of as pub/sub processor, broadcast `payloads` to registered callback function) and `NodeJS`'s `EventEmitter` to implement Flux.	

## Flux Concepts Introduction
![React Flux](./images/flux-simple-diagram.png "React Flux")

There are four roles in the world of Flux Unidirectional Data Flow, each of which has different responsibilities.

1. actions / Action Creator 

	Actions define behaviors that change states. They allow developers to quickly understand all functionalities of Apps. If you want to change states, you can only perform actions. Actions can be both synchronous and asynchronous (for example, invocatin of asynchonous API to retrieve data).

	Actions works together with action creators. Action represent objects that change states; action creators pass actions to dispatcher. Normally Flux standard action would look like the code below, with `type` identifying each action and `payload` carrying data:

	```
	// action
	const addTodo = {
	  type: 'ADD_TODO',
	  payload: {
	    text: 'Do something.'  
	  }
	}

	AppDispatcher.dispatch(addTodo);
	```

	When the promise is rejected:

	```
	{
	  type: 'ADD_TODO',
	  payload: new Error(),
	  error: true
	}
	```

2. Dispatcher

	`Dispatcher` is the core of Flux. Every app has only one dispatcher, providing for APIs that allows for store to register `callback function`, and is responsible for sending action events to all the stores. In our example, we use Facebook 提Dispatcher API including `dispatch` and `subscribe`.

3. Stores

	An app usually has multiple stores to keep business logic, for example: TodoStore, RecipeStore. Stores keeps data and provider `listener`s for `view`s. If data change, views will be udpated. Please note stores only provide `getter API`s for data retrival. Change of states need actions.

4. Views (Controller Views)

	It's under the purview of `React`, which provides `callback function`s, and changes `View`.

## Flux Flow Review

![React Flux](./images/flux-react.png "React Flux")

Flux 架構前置作業：

1. Stores 向 Dispatcher 註冊 callback，當資料改變時告知 Stores
2. Controller Views 向 Stores 取得初始資料
3. Controller Views 將資料給 Views 去渲染 UI
4. Controller Views 向 store 註冊 listener，當資料改變時告知 Controller Views 

Flux 與使用者互動運作流程：

1. 使用者和 App 互動，觸發事件，Action Creator 發送 actions 給 Dispatcher
2. Dispatcher 依序將 action 傳給 store 並由 action type 判斷合適的處理方式
3. 若有資料更新則會觸發 Controller Views 向 store 註冊的 listener 並向 store 取得更新資料
4. View 根據 Controller Views 的新資料重新繪製 UI

## Flux 實戰初體驗
介紹完了整個 Flux 基本架構後，接下來我們就來動手實作一個簡單 Flux 架構的 Todo，讓使用者可以在 `input` 輸入代辦事項並新增。

首先，我們先完成一些開發的前置作業，先透過以下指令在根目錄產生 npm 設定檔 `package.json`：

```
$ npm init
```

安裝相關套件（包含開發環境使用的套件）：

```
$ npm install --save react react-dom flux events
```

```
$ npm install --save-dev babel-core babel-eslint babel-loader babel-preset-es2015 babel-preset-react eslint eslint-config-airbnb eslint-loader eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react html-webpack-plugin webpack webpack-dev-server
```

安裝好後我們可以設計一下我們的資料夾結構，首先我們在根目錄建立 `src`，放置 `script` 的 `source` 。在 `components` 資料夾中我們會放置所有 `components`（個別元件資料夾中會用 `index.js` 輸出元件，讓引入元件更簡潔），另外還有 `actions`、`constants`、`dispatcher`、`stores`，其餘設定檔則放置於根目錄下。

![React Flux 資料夾結構](./images/folder.png "React Flux 資料夾結構")

接下來我們參考上一章設定一下開發文檔（`.babelrc`、`.eslintrc`、`webpack.config.js`）。這樣我們就完成了開發環境的設定可以開始動手實作 `React Flux` 應用程式了！

HTML Markup：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>TodoFlux</title>
</head>
<body>
    <div id="app"></div>
</body>
</html>
```

以下為 `src/index.js` 完整程式碼，安排了父 `component` 和在 HTML Markup 插入位置：

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import TodoHeader from './components/TodoHeader';
import TodoList from './components/TodoList';

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {};
  }
  render() {
    return (
      <div>
        <TodoHeader />
        <TodoList />
      </div>
    );
  }
}

ReactDOM.render(<App />, document.getElementById('app'));
```

通常實務上我們會開一個 `constants` 資料夾存放 `config` 或是 `actionTypes` 常數。以下是 `src/constants/actionTypes.js`：

```javascript
export const ADD_TODO = 'ADD_TODO';
```

在這個範例中我們繼承了 Facebook 提供的 Dispatcher API（主要是繼承了 `dispatch`、`register` 和 `subscribe` 的方法），打造自己的 DispatcherClass，當使用者觸發 `handleAction()` 會 `dispatch` 出事件。以下是 `src/dispatch/AppDispatcher.js`：

```javascript
// Todo app dispatcher with actions responding to both
// view and server actions
import { Dispatcher } from 'flux';

class DispatcherClass extends Dispatcher {
  handleAction(action) {
    this.dispatch({
      type: action.type,
      payload: action.payload,
    });
  }
}

const AppDispatcher = new DispatcherClass();

export default AppDispatcher;
```

以下是我們利用 `AppDispatcher` 打造的 `Action Creator` 由 `handleAction` 負責發出傳入的 `action` ，完整程式碼如 `src/actions/todoActions.js`：

```javascript
import AppDispatcher from '../dispatcher/AppDispatcher';
import { ADD_TODO } from '../constants/actionTypes';

export const TodoActions = {
  addTodo(text) {
    AppDispatcher.handleAction({
      type: ADD_TODO,
      payload: {
        text,
      },
    });
  },
};
```

`Store` 主要是負責資料以及業務邏輯處理，我們繼承了 `events` 模組的 `EventEmitter`，當 `action` 傳入 `AppDispatcher.register` 的處理範圍後，根據 `action type` 選擇適合處理的 `store` 進行處理，處理完後透過 `emit` 方法發出事件讓監聽的 `Views Controller` 知道。以下是 `src/stores/TodoStore.js`：

```javascript
import AppDispatcher from '../dispatcher/AppDispatcher';
import { ADD_TODO } from '../constants/actionTypes';
import { EventEmitter } from 'events';

const store = {
  todos: [],
  editing: false,
};

class TodoStoreClass extends EventEmitter {
  addChangeListener(callback) {
    this.on(ADD_TODO, callback);
  }
  removeChangeListener(callback) {
    this.removeListener(ADD_TODO, callback);
  }
  getTodos() {
    return store.todos;
  }
}

const TodoStore = new TodoStoreClass();

AppDispatcher.register((action) => {
  switch (action.type) {
    case ADD_TODO:
      store.todos.push(action.payload.text);
      TodoStore.emit(ADD_TODO);
      break;
    default:
      return true;
  }
  return true;
});

export default TodoStore;
```

在這個 React Flux 範例中我們把 `View` 和 `Views Controller` 整合在一起。在 `TodoHeader` 中，我們主要任務是讓使用者可以透過 `input` 新增代辦事項。使用者輸入文字在 `input` 時會觸發 `onChange` 事件，進而更新內部的 `state`，當使用者按了送出鈕就會觸發 `onAdd` 事件，`dispatch` 出 `addTodo event`。以下是 `src/components/TodoHeader.js` 完整範例：

```javascript
import React, { Component } from 'react';
import { TodoActions } from '../../actions/todoActions';

class TodoHeader extends Component {
  constructor(props) {
    super(props);
    this.onChange = this.onChange.bind(this);
    this.onAdd = this.onAdd.bind(this);
    this.state = {
      text: '',
      editing: false,
    };
  }
  onChange(event) {
    this.setState({
      text: event.target.value,
    });
  }
  onAdd() {
    TodoActions.addTodo(this.state.text);
    this.setState({
      text: '',
    });
  }
  render() {
    return (
      <div>
        <h1>TodoFlux</h1>
        <div>
          <input
            value={this.state.text}
            type="text"
            placeholder="請輸入代辦事項"
            onChange={this.onChange}
          />
          <button
            onClick={this.onAdd}
          >
            送出
          </button>
        </div>
      </div>
    );
  }
}

export default TodoHeader;
```

在上面的 Component 中我們讓使用者可以新增代辦事項，接下來我們要讓新增的代辦事項可以顯示。我們在 `componentDidMount` 設了一個監聽器 `TodoStore` 資料改變時會去把資料重新再更新，這樣當使用者新增代辦事項時 `TodoList` 就會保持同步。當以下是 `src/components/TodoList.js` 完整程式碼：

```javascript
import React, { Component } from 'react';
import TodoStore from '../../stores/TodoStore';

function getAppState() {
  return {
    todos: TodoStore.getTodos(),
  };
}
class TodoList extends Component {
  constructor(props) {
    super(props);
    this.onChange = this.onChange.bind(this);
    this.state = {
      todos: [],
    };
  }
  componentDidMount() {
    TodoStore.addChangeListener(this.onChange);
  }
  onChange() {
    this.setState(getAppState());
  }
  render() {
    return (
      <div>
        <ul>
          {
            this.state.todos.map((todo, key) => (
              <li key={key}>{todo}</li>
            ))
          }
        </ul>
      </div>
    );
  }
}

export default TodoList;
```

若讀者都有跟著上面的步驟走完的話，最後我們在終端機的根目錄位置執行 `npm start` 就可以看到整個成果囉，YA！
![React Flux ](./images/flux-demo.png "React Flux ")

## Summary
Flux Advantage:

1. Help developers understand behaviors of the applications;
2. Data and business logic are well managed;
3. View responsible for only UI, no responsibility for state management;
4. Clear Architecture makes for easier management of large scale progrects.

Flux Disadvantage:

1. Program code is verbose;
2. Complex for small scale applications.

以上就是 Flux 的實戰入門，我知道一開始接觸 Flux 的讀者一定會覺得很抽象，有些讀者甚至會覺得這個架構到底有什麼好處（明明感覺沒比 MVC 高明到哪去或是一點都不簡潔），但如同上述優點所說 Flux 設計模式的優勢在於清楚的架構和分工對於複雜中大型應用程式易於維護和管理程式碼。若還是不熟悉的讀者可以跟著範例多動手，相信慢慢就可以體會 Flux 的特色。事實上，在開發社群中為了讓 Flux 架構更加簡潔，產生了許多 Flux-like 的架構和函式庫，接下來將帶讀者們進入目前最熱門的架構：`Redux`。

## Further Reading
1. [Getting To Know Flux, the React.js Architecture](https://scotch.io/tutorials/getting-to-know-flux-the-react-js-architecture)
2. [Flux Official Site](https://facebook.github.io/flux/)
3. [Comparing Flux and MVC and Introduction to Flux](http://blog.techbridge.cc/2016/04/29/introduce-flux-from-flux-and-mvc/)
4. [Flux Stores and ES6](https://medium.com/@softwarecf/flux-stores-and-es6-9b453dbf9db#.uuf1ddj8u)
5. [React and Flux: Migrating to ES6 with Babel and ESLint](https://medium.com/front-end-developers/react-and-flux-migrating-to-es6-with-babel-and-eslint-6390cf4fd878#.vafamphwy)
6. [Building an ES6/JSX/React Flux App – Part 2 – The Flux](https://shellmonger.com/2015/08/17/building-an-es6jsxreact-flux-app-part-2-the-flux/)
7. [Question: How to choose between Redux's store and React's state? #1287](https://github.com/reactjs/redux/issues/1287)
8. [acdlite/flux-standard-action](https://github.com/acdlite/flux-standard-action)

（image via [devjournal](http://devjournal.ru/wp-content/uploads/2016/03/React.js-Flux-Redux.png)、[facebook](https://facebook.github.io/flux/)、[scotch.io](https://cask.scotch.io/2014/10/V70cSEC.png)）

## :door: Dokodemo Door
| [Back to Main Page](https://github.com/druckenclam/reactjs101/tree/en) | [Previous Chapter: ImmutableJS Introductory Tutorial](https://github.com/druckenclam/reactjs101/blob/en/Ch06/react-immutable-introduction.md) | [Next Chapter：Redux Basics](https://github.com/druckenclam/reactjs101/blob/en/Ch07/react-redux-introduction.md) |

| [Correction, Questions and Wish List](https://github.com/kdchang/reactjs101/issues) |
