# React基础

~~~
<Fragment></Fragment> => <template></template>
~~~

点击其他地方input聚焦

~~~~
<label htmlFor='sufan'>点击一下</label>
<input id='sufan'>
~~~~

**推荐使用在构造函数中绑定this**

~~~
constructor(props) {
  super(props);
  this.handleClick = this.handleClick.bind(this)
}
~~~

**单向数据流**
不可以在子组件进行修改
为什么使用单向数据流？
子组件在父组件中调用好多次，为了同时数据发生改变，如果不是单向数据流会导致数据冲突
**react和jquery结合使用**
是可以进行结合使用的
**函数式编程**
1. 代码更加清晰，不同的函数处理的功能不同
2. 更好的进行自动化测试

##校验和默认props

~~~
import PropTypes from 'prop-types';
XiaojiejieItem.propTypes={
    content:PropTypes.string.isRequired,
    deleteItem:PropTypes.func,
    index:PropTypes.number
}
XiaojiejieItem.defaultProps = {
    avname:'松岛枫'
}
~~~

## ref

~~~
<ul ref='(ul) => {this.ul = ul}'>
this.ul.querySelectorAll('li').length
~~~

**解决setState异步的问题**
~~~
addList(){
    this.setState({
        list:[...this.state.list,this.state.inputValue],
        inputValue:''
    },()=>{
        console.log(this.ul.querySelectorAll('div').length)
    })
}
~~~

## React生命周期
### initialization(组件初始化)
setup props and state
### mounting(加载)
~~~~
componentWillMount(){
    console.log('componentWillMount----组件将要挂载到页面的时刻')
}
render(){
    console.log('render---组件挂载中.......')
}
componentDidMount(){
    console.log('componentDidMount----组件挂载完成的时刻执行')
}

~~~

### Updation(更新)
**属性发生改变的时候生命周期(props)**

~~~
组件第一次存在于dom中，函数是不会执行的
如果已经存在于Dom中，函数才会执行
componentWillReceiveProps() {

}
shouldComponentUpdate(){
    console.log('shouldComponentUpdate---组件发生改变前执行')
    return true
}
componentWillUpdate(){
    console.log('componentWillUpdate---组件更新前，shouldComponentUpdate函数之后执行')
}
render(){
    console.log('render---组件挂载中.......')
}
componentDidUpdate(){
    console.log('componentDidUpdate----组件更新之后执行')
}
~~~

**state发生改变的时候生命周期**
~~~
shouldComponentUpdate(){
    console.log('shouldComponentUpdate---组件发生改变前执行')
    return true
}
componentWillUpdate(){
    console.log('componentWillUpdate---组件更新前，shouldComponentUpdate函数之后执行')
}
render(){
    console.log('render---组件挂载中.......')
}
componentDidUpdate(){
    console.log('componentDidUpdate----组件更新之后执行')
}
~~~

现在就可以在控制台console里看到结果了，并且结果是每次文本框发生改变时都会随着改变。如果你返回了false，这组件就不会进行更新了。 简单点说，就是返回true，就同意组件更新;返回false,就反对组件更新。

### Unmounting(卸载)

~~~
componentWillUnmount() {
  console.log('组件被删除')
}
~~~

## 组件的优化
~~~
shouldComponentUpdate(nextProps,nextState){
    if(nextProps.content !== this.props.content){
        return true
    }else{
        return false
    }

}
~~~

## 动画

ease-in 由快到慢
~~~
.hide {opacity: 0; transition: all 1.5s ease-in}
~~~
### 帧动画
`forwards` 结束执行不会返回到0%
~~~
.hide {animation: hide-item 2s ease-in forwards}
@keyframe hide-item {
  0% {

  }
  50% {

  }
  100% {

  }
}
~~~

### react-transition-group(官方的动画库)
- Transiton
- CSSTransition
- TransitionGroup

appear= true 出现时候产生
unmountOnExit 删除的时候不占用dom
Key 循环的时候绑定key
~~~
function App() {
  const [inProp, setInProp] = useState(false);
  return (
    <div>
      <CSSTransition in={inProp} timeout={200} classNames="my-node">
        <div>
          {"I'll receive my-node-* classes"}
        </div>
      </CSSTransition>
      <button type="button" onClick={() => setInProp(true)}>
        Click to Enter
      </button>
    </div>
  );
}
~~~

~~~
.my-node-enter {
  opacity: 0;
}
.my-node-enter-active {
  opacity: 1;
  transition: opacity 200ms;
}
.my-node-exit {
  opacity: 1;
}
.my-node-exit-active {
  opacity: 0;
  transition: opacity 200ms;
}
~~~

# Redux

**redux注意事项**
- store必须是唯一的，多个store是坚决不允许的，只能有一个store空间
- 只有store能改变自己的内容， reducer不能改变
- reducer必须是纯函数

## 拓展中间件(利用增强函数)
增强函数: 一个方法执行两个函数
`compose` react中提供的使用增强函数

~~~
import { createStore, applyMiddleware, compose } from "redux";
import thunk from 'redux-thunk'
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?                           window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;
const enhancer = composeEnhancers(applyMiddleware(thunk))
const store = createStore(
  reducer,
  enhancer
)
~~~

## thunk中异步获取数据
**配置thunk**
store中index.js
~~~~
import { createStore, applyMiddleware, compose } from "redux";
import thunk from 'redux-thunk'
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?                           window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;
const enhancer = composeEnhancers(applyMiddleware(thunk))
const store = createStore(
  reducer,
  enhancer
)
~~~~

**actionTypes.js**
~~~
export const GET_LIST = 'GET_LIST'
~~~

**actionCreators.js**
~~~
export const getTodoList = () => {
  //  用thunk中间件的时候在action中调用api
  return (dispatch) => {
    axios.get('https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList').then((res)=>{
      dispatch(getListAction(res.data))
    })
  }
}
~~~

**reducer.js**
~~~
if(action.type === GET_LIST) {
  let newsState = JSON.parse(JSON.stringify(state))
  newsState.dataList = action.data.data.list
  return newsState
}
~~~

**TodoList.js中调用getTodoList**
~~~
componentDidMount() {
  store.dispatch(getTodoList());
}
~~~

## redux-saga中异步获取数据
**配置redux-saga**
redux-saga中必须有一个启动函数,启动函数必须是generentor函数
~~~
import { createStore, applyMiddleware, compose } from "redux";
import reducer from "./reducer";
// import thunk from 'redux-thunk'
import createSagaMiddleware from "redux-saga";
import mySages from './sagas'

const sagaMiddlware = createSagaMiddleware();

// 增强函数
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?                           window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;

const enhancer = composeEnhancers(applyMiddleware(sagaMiddlware))

const store = createStore(
  reducer,
  enhancer
)

sagaMiddlware.run(mySages)

export default store
~~~

**mySages.js配置**
~~~
// takeEvery用来监听是否是用action
// 利用put来进行数据的改变相当于dispatch
import { takeEvery, put } from 'redux-saga/effects';
import { GET_MY_LIST } from "./actionsTypes";
import { getListAction } from "./actionCreators";
import axios from 'axios';

function* mySaga() {
  yield takeEvery(GET_MY_LIST, getList)
}

function* getList(id) {
  const res = yield axios.get('https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList')
  yield put(getListAction(res.data))
}

export default mySaga
~~~

**actionTypes.js**
~~~
export const GET_MY_LIST = 'GET_MY_LIST'
~~~

**actionsCreator.js**
~~~
export const getMyList = (data) => ({
  type: GET_MY_LIST,
  data
})
~~~

**TodoList.js中调用**
~~~
componentDidMount() {
  store.dispatch(getMyList('11111111111'));
}
~~~

# reactRouter
## 动态传值
- 设置规则
- 传递值
- 接收值
- 显示内容

### 重定向Redirect

**标签式重定向**
~~~
import React, { Component } from 'react';
import { Link, Redirect } from "react-router-dom";

class Index extends Component {
  constructor(props) {
    super(props)
    this.state = {
      list: [
        {
          cidd: 123,
          title: '苏凡gihthub'
        },
        {
          cidd: 234,
          title: 'gihthub'
        },
        {
          cidd: 345,
          title: 'thub'
        }
      ]
    }
  }
  render() {
    return (
      <div>
        <Redirect to='/home/'>
          <h2>苏凡</h2>
          <ul>
            {
              this.state.list.map((item,index) => {
                return (
                  <li key={index}>
                    <Link to={'/list/'+item.cidd}>{item.title}</Link>
                  </li>
                )
              })
            }
          </ul>
        </Redirect>
      </div>
    );
  }
}

export default Index;
~~~

~~~
import React from 'react'
import { BrowserRouter as Router, Route, Link } from "react-router-dom";
import Index from "./Pages/index";
import List from "./Pages/List";
import Home from "./Pages/Home"



function AppRouter() {
  return (
    <Router>
      <ul>
        <li><Link to='/'>首页</Link></li>
        <li><Link to='/list/111'>列表</Link></li>
      </ul>
      <Route path='/' exact component={Index}/>
      <Route path='/list/:id' component={List}/>
      <Route path='/home/' component={Home}/>
    </Router>
  )
}

export default AppRouter
~~~

**编程式重定向**

~~~
this.props.history.push("/home/")
~~~
