---
title: 在哪获取数据：componentWillMount vs componentDidMount
date: 2017-09-11 16:46:49
tags: [React]
categories: "技术"
---

> 当你需要为 React 组件获取一些数据的时候，你会在哪做呢？

这个问题一直都存在。
<!--more-->

获取数据有两个常见的地方可以做到：

- componentWillMount
- componentDidMount

我们应该都很清楚，`render` 方法肯定不是一个获取数据的好地方 - 或者做任何有关异步操作的事情，它会以某种方式改变 state，也可能会引起副作用。

让我们来看看这两种常见的选项以及他们的优缺点。

### componentWillMount

这个方法在组件第一次 render 之前被调用，所以猛一看会觉得这是写数据请求逻辑的最好的地方。

但是这里有一个“陷阱”：在组件被渲染之前获取数据的异步调用不会 return 出来数据。就是说组件将至少一次会伴随着空数据被渲染。

这是没有办法为了等待数据返回而去“暂停”渲染。你不能用 `setTimeout` 方法从 `componentWillMount` 中 return 出一个 promise。 处理这个问题的正确方式是[设置组件的初始 state](https://daveceddia.com/watch-out-for-undefined-state/) 来确保组件的有效渲染。

正好，在 ES6 风格的 `class` 组件中，它的构造函数和 `componentWillMount` 的功能是一样的，所以，如果你已经有一个构造函数方法，那就可以直接把代码写在这里。


### componentDidMount

等到 `componentDidMount` 被调用的时候，组件已经被渲染一次了。

通过实践，`componentDidMount` 是**发送数据请求的最好的地方**，有以下两个原因：

1、使用 **Did**Mount 能够清晰的看出，直到初始渲染之后，数据才会被加载。这就提醒你要适当的去设置初始的 `state`，就不会因为 [state 为 undefined 而导致错误](https://daveceddia.com/watch-out-for-undefined-state/)

2、如果你需要在服务端（SSR/同构/其他的流行用语）渲染应用，那么 `componentWillMount` 将会被调用**两次** —— 一次在服务端，一次在客户端，当然，这应该是你不愿意看到的。那么把数据加载的代码放在 `componentDidMount` 中能确保只会从客户端获取数据。

### Wrap Up

我希望 **在哪** 加载数据的这个问题已经讲清楚了。如果你仍然不确定调用 AJAX 请求和加载数据的最好方法是什么，请阅读我的这篇文章 [在 React 中发送 AJAX 请求](https://daveceddia.com/ajax-requests-in-react/)
