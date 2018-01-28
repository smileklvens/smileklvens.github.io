---
layout:     post
title:      React-Redux 简单实用
subtitle:   react全家桶，react-router，React-Redux，react-native
date:       2017-8-10
author:     klvens
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - RN
---

## React-Redux 将所有组件分成两大类。
UI 组件（presentational component）和容器组件（container component）。

### UI 组件有以下几个特征。
		* 只负责 UI 的呈现，不处理任何业务逻辑
		* 没有状态（不能使用this.state这个变量）
		* 所有数据都由参数（this.props）提供
		* 不使用任何 Redux 的 API
比如：
```const Title =
  value => <Text>{value}</Text>;
```
### 容器组件正好相反。
		* 负责管理数据和业务逻辑，但不负责 UI 的呈现
		* 带有内部状态
		* 使用 Redux 的 API

##  connect()  用于将UI 组件生成容器组
mapStateToProps前者负责输入逻辑，即将state映射到 UI 组件的参数（props），mapDispatchToProps，即将用户对 UI 组件的操作映射成 Action。

```import { connect } from 'react-redux'

const VisibleBusinessList = connect(
  mapStateToProps,
  mapDispatchToProps
)(BusinessList)
```
## <Provider> 组件
React-Redux 提供Provider组件，可以让容器组件拿到state

## 使用步骤
比如现有如下组件
```
class Calculation extends Component {
  render() {
    const { value, onAddClick } = this.props
    return (
      <View>
        <Text>{value}</Text>
        <Button onClick={onAddClick}>add</Button>
      </View>
    )
  }
} 
```
*  UI 组件有两个参数：value和onAddClick。前者需要从state计算得到，后者需要向外发出 Action
*  定义value到state的映射，以及onAddClick到dispatch的映射。
``` 
function mapStateToProps(state) {
  return {
    value: state.count
  }
}
function mapDispatchToProps(dispatch) {
  return {
    onAddClick: () => dispatch(inAddAction)
  }
}
```
// Action Creator
```
const inAddAction = { type: 'add' }`
```
* connect方法生成容器组件
`const App = connect(
  mapStateToProps,
  mapDispatchToProps
)(Calculation)`
* 定义这个组件的 Reducer
```
function computer(state = { count: 0 }, action) {
  const count = state.count
  switch (action.type) {
    case 'add':
      return { count: count + 1 }
    default:
      return state
  }
}
```

* 最后，生成store对象，并使用Provider在根组件外面包一层。
```
const persistedState = loadState();
const store = createStore(
  todoApp,
  persistedState
);

store.subscribe(throttle(() => {
  saveState({
    todos: store.getState().todos,
  })
}, 1000))

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

## 如有疏漏，请指出，谢谢

