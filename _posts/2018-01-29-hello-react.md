---
layout: wp
title: Hello,React
comments: true
---

### 认知 ###

从`jquery`到`angularjs`，再切换到`react`你能感到一种「工程方式」的转变。在 react 全家桶下，写的代码更像是照着「模具」画像（工程感更强烈）。React 的一些新认知：  

`组件和DOM树`: 浏览器把CSS和HTML解析成DOM树，而React同样把HTML结构解析成了DOM数，不过更加轻量级。DOM树的每一个节点变化由React对象管理。    
`JSX`: React的开发语言，混合HTML和JS，`<`开头用HTML解析，`{`用JS解析。    

### 渲染与事件 ###  

[props](https://reactjs.org/docs/components-and-props.html): DOM的属性；     
state: model存储，保存当前的状态;   

组件包括 mount(渲染到dom tree)->update->unmount   

componentWillMount: render 之前。预处理操作，一般没啥用。   
>The componentDidMount() hook runs after the component output has been rendered to the DOM.    

render: 渲染函数。   

componentDidMount: render之后，  

>componentDidMount() is invoked immediately after a component is mounted. Initialization that requires DOM nodes should go here. If you need to load data from a remote endpoint, this is a good place to instantiate the network request.



componentWillReceiveProps:   

>componentWillReceiveProps() is invoked before a mounted component receives new props. If you need to update the state in response to prop changes (for example, to reset it), you may compare this.props and nextProps and perform state transitions using this.setState() in this method.

     
setState:    

>setState() enqueues changes to the component state and tells React that this component and its children need to be re-rendered with the updated state. This is the primary method you use to update the user interface in response to event handlers and server responses.


### redux ###

store: 对应props,用来共享状态。   
state: store中对应的数据。   
action: view的操作对应的通知类型,用于改变state, store.dispatch 来发出action。   
reducer:处理action产生新的state.  
   


### 参考资料 ###

[1 浏览器渲染](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/constructing-the-object-model?hl=zh-cn)   

