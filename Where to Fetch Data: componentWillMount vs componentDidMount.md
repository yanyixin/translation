---
title: Where to Fetch Data: componentWillMount vs componentDidMount
date: 2017-09-11 16:46:49
tags: [React]
categories: "技术"
---

> When you need to fetch some data for a React component, where do you do it?

This question comes up all the time.


There are two common places to fetch data:

- componentWillMount
- componentDidMount

And just so we’re clear, the `render` function is never a good place to fetch data – or to do anything that’s asynchronous, that changes state in some way, or that causes side effects.


Let’s look at the two common options and the pros and cons of each.

### componentWillMount

This function is called right before the component’s first render, so at first glance it appears to be a perfect place to put data fetching logic.

There’s a “gotcha,” though: An asynchronous call to fetch data will not return before the render happens. This means the component will render with empty data at least once.

There is no way to “pause” rendering to wait for data to arrive. You cannot return a promise from `componentWillMount` or wrangle in a `setTimeout` somehow. The right way to handle this is to [setup the component’s initial state](https://daveceddia.com/watch-out-for-undefined-state/) so that it’s valid for rendering.

Incidentally, in ES6-style `class` components, the constructor plays the same role as `componentWillMount`, so if you already have a constructor, you can just put the code there.

### componentDidMount

By the time `componentDidMount` is called, the component has been rendered once.

In practice, `componentDidMount` is **the best place to put calls to fetch data,** for two reasons:

1. Using **Did**Mount makes it clear that data won’t be loaded until after the initial render. This reminds you to set up initial `state` properly, so you don’t end up with [undefined state that causes errors](https://daveceddia.com/watch-out-for-undefined-state/).

2. If you ever need to render your app on the server (SSR/isomorphic/other buzzwords), `componentWillMount` will actually be called *twice* – once on the server, and again on the client – which is probably not what you want. Putting the data loading code in `componentDidMount` will ensure that data is only fetched from the client.

### Wrap Up

I hope this clears up the question of *where* to load data. If you’re still not sure of the best way how to actually make the AJAX call and load data, read my post [AJAX Requests in React](https://daveceddia.com/ajax-requests-in-react/).
