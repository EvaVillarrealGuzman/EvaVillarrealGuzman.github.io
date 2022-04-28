---
title: Optimizing Performance in React
layout: post
subtitle: We will see different techniques and tools that will help you improve the performance of your React applications
categories: react
tags: [react, performance]
---

Performance has an impact on the quality of the application and the user experience. There are different decisions that impact the overall performance of an application, from how we code to how the application infrastructure is configured. In this post we will focus on how to write our React applications to optimize them. Internally, React uses several techniques to minimize the number of costly DOM operations required to update the UI. This will lead to a fast user interface without doing much work to specifically optimize for performance. Nevertheless, there are several ways you can speed up your React application.

## React.Fragment

React requires that you always group a set of elements into a parent element. It is common for React developers that not know ```Fragment``` to wrap with the ```div``` element, as shown in the following example:

```jsx
<div>
    <h1>Title</h1>
    <p>Content</p>
</div>
```

As this element ```div``` has no functionality other than grouping elements, this can cause performance issues as it adds an extra node to DOM. React provides Fragments for this, which doesn't any extra node. There're two syntaxes for Fragments, one is using ```<React.Fragment></React.Fragment>``` or with the short syntax ```<></>```. The above example can be written with Fragment in the following ways:

```jsx
<React.Fragment>
    <h1>Title</h1>
    <p>Content</p>
</React.Fragment>
```

```jsx
<>
    <h1>Title</h1>
    <p>Content</p>
</>
```

## Use packaging in Production mode

When you're developing a React application, helpful warnings and error messages are presented. These help you to identity bugs and problems during the development, but they also come at a performance cost.

If you look at the React source code, you'll see a lots of code with ```if (process.env.NODE_ENV != 'production')```. This snippets of code that React is executing in your development environment is not something that the user needs. For production environments this code is unnecessary. You can achieve this in different ways depending on how you built your app:

- If you built your project with create-react-app, so you can run ``npm run build`` to build the production code. 
- If you used webpack, so you can run ```webpack -p``` which is the same as ```webpack --optimize-minimize --define process.env.NODE_ENV="'production'"```

## Use memoization

Memoization is an optimization technique to increase the performance of the application by storing the results and returning the cached result when the inputs are the sames.

React provides React.memo and useMemo for memoization which store the cached components.

When functional components is rendered using React.memo or useMemo then its results are saved in memory and next time that the component getting called with the same props, the result is a cache component without any execution.

```jsx
const UserDisplay = (userDetails) =>{
    const {name, age, address} = userDetails;

    return (
        <>
            <h4>{name}</h4>
            <p>{age} , {address}</p>
        </>
    )
}
export default React.memo(UserDisplay);
// 1 - UserDisplay component gets called and executed, and then rendered
<UserDisplay
  name="Test"
  age="30"
  address="Test address"
/>
// 2 - The cached result will render without any execution
<UserDisplay
  name="Test"
  age="30"
  address="Test address"
/>
// Third - UserDisplay component gets called and executed, and then rendered
// because the *name* value is different
<UserDisplay
  name="New Test"
  age="30"
  address="Test address"
/>
``` 

## Code Splitting

Before explaining *code splitting* it's important to undertand the concept of *Lazy loading*. This means that the resources (images, script, etc.) should be loaded when they are really needed. So instead of loadind the whole page and rendering in the browser, only the critical components are loaded first and then the rest only when they needed. In this way, time and memory are not wasted with processes that do not benefit the user experience.

Now, *code splitting* es a technique that allow us to achieve the *lazy loading* concept by splitting the application into different files instead of delivering one file. There are different criteria to split the file. Some of the most common and that can be combined are:

**1- Split by routes:**

This will leaves with different file for each top level route. In the next example, you will have three files: one for the DynamicPage component, one for the NoMatch component and one for the main application.

```jsx
import React, {Suspense} from 'react'
import {Switch, BrowserRouter as Router, Route} from 'react-router-dom';
import importedComponent from 'react-imported-component';

import Home from './Home';
import Loading from './Loading';

const AsyncDynamicPAge = React.lazy(() => import(/* webpackChunkName:'DynamicPage' */ './DynamicPage'));
const AsyncNoMatch = React.lazy(() => import(/* webpackChunkName:'NoMatch' */ './NoMatch'));

const App = () => {
    return (
        <Router>
            <Suspense fallback={Loading}>
                <Switch>
                    <Route exact path="/" component={Home}/>
                    <Route exact path="/dynamic" component={AsyncDynamicPAge}/>
                    <Route component={AsyncNoMatch}/>
                </Switch>
            </Suspense>
        </Router>
    )
        ;
};

export default App;
``` 

The way to indicate webpack that we want to split the code into different files is through *dynamic imports* with the keyword *import*.

```React.lazy()``` is a function that allow you to implement the *dynamic imports* for components in React. ```React.Suspense``` has a *fallback* property that receive a React element that you want to render while the component is loading with ```React.lazy()```.

Also, you will have to configure the webpack file as follows:

```json
...
module.exports = {
  ...
  output: {
    filename: '[name].[hash].js',
    ...
  },
}
``` 

**2- Split by vendor:**

This approach will help you in two ways: it helps the browser to store those resources that change with less frequency; and it help the browser to take advantage of parallel downloading to potencially reduce load time.

An example of a webpack configuration is as follows:

```jsx
...
module.exports = {
  entry: {
    vendor: ['semantic-ui-react'],
    app: './src/index.js'
  },
  ...
  optimization: {
    splitChunks: {
      cacheGroups: {
        styles: {
          name: 'styles',
          test: /\.css$/,
          chunks: 'all',
          enforce: true
        },
        vendor: {
          chunks: 'initial',
          test: 'vendor',
          name: 'vendor',
          enforce: true
        }
      }
    }
  },
  ...
};
``` 

The ```entry.vendor: [‘semantic-ui-react’]```  specifies which vendor we want to extract from our main application. The ```optimization``` will help you to reduce file size.

**3- Extract css styles:**

You can use ExtractTextWebpackPlugin in webpack to extract all css code into a separate css file. This will also help the browser to cache this file and do parallel downloading on page load.

## Virtualizing the long lists

If your application renders long lists of data, it's recommend that you use the *windowing* technique. This only renders a subset at any time and render the remaining data whenever scrolled, and reduce the re-rendering time, as well as the number of nodes created in the DOM.

[react-window](https://react-window.vercel.app/#/examples/list/fixed-size) and [react-virtualized](https://bvaughn.github.io/react-virtualized/#/components/List) are popular libraries of *windowing* in React.

## Implement Throttling and Debouncing to avoid multiple API calls

Debouncing and Throttling are widely-used techniques that help us in limiting the rate at which a function fires off. These two techniques give us a layer of control between the event and the execution of the functions attached to them. API servers often implement either of these two techniques to prevent the application from being overloaded.

Both these techniques are almost identical and help us reduce the number of function calls being made but they have one small, but significant difference among them. 

Debouncing is a technique in which no matter how many times a user fires an event, the call will be made only after a specific amount of time has passed after the user stops firing the event.For example, let's say a user is typing something in a search box. This search box makes API calls and has a debounce function attached to it with a specified time duration of 400ms. So now, unless 400ms have passed after the user stopped typing, the API call wouldn't be made.

Throttling is a technique that makes the next function call strictly after a certain period of time. No matter how many times the user fires an event, the function attached will be executed only once in the given time period.

Debouncing can be used when the result of the most recent event occurrence is what is important. For example, a search query on an e-commerce website.

Throttling can be used when the input provided to the function call doesn't matter or is the same each time. For example, infinite scrolling on a webpage. Here, we need to check how far the user is from the bottom of the page. If they're too close, we request more data and append it to the page. Here debouncing wouldn't work as it would only trigger the event when the user has stopped scrolling but we need to start fetching the content before the user reaches the bottom.

## Enable Gzip on your web server

All modern browser support and automatically negotiate Gzip compression for the HTTP requests. Enabling Gzip compression will help reduce the size of responses, which will impact resource download time. 

Check your web server documentation to enable compression:

- Apache: Use [mod_deflate](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
- Nginx: Use   [ngx_http_gzip_module](https://nginx.org/en/docs/http/ngx_http_gzip_module.html)


## Useful tools

We can't talk about performance if we don't measure it. We need to perform comparisons before and after making changes to your code to track your progress.

Here some tools that can help you with this:

### Why Did You Render

[Why Did You Render](https://www.npmjs.com/package/@welldone-software/why-did-you-render) is a library that helps detect why a component is re-rendering. It does this by modifying the behavior of the code at runtime using *monkey patching* React.

### React Profiler

[React Profiler](https://reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html) is a DevTools that you can install in your browser and collect timing information about each component that's rendered.

### Webpack Bundle Analyzer

[Webpack Bundle Analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer) provide you a visual representation about the sizes of your files.

## Sources

- [Lessons Learned: Code Splitting with Webpack and React](https://hackernoon.com/lessons-learned-code-splitting-with-webpack-and-react-f012a989113)
- [How to use Webpack with React: an in-depth tutorial](https://www.freecodecamp.org/news/learn-webpack-for-react-a36d4cac5060/)
- [Optimizando el Rendimiento](https://es.reactjs.org/docs/optimizing-performance.html)
- [Performance Optimization Techniques For React Applications](https://www.c-sharpcorner.com/article/performance-optimization-techniques-for-react-applications/)
- [Improve Your React App Performance by Using Throttling and Debouncing](https://blog.bitsrc.io/improve-your-react-app-performance-by-using-throttling-and-debouncing-101afbe9055)
- [9 Performance Optimization Strategies for ReactJS Development](https://medium.com/swlh/9-performance-optimization-strategies-for-reactjs-development-36f6d03a0912)
- [Componentes React Eficientes: Una Guía para Optimizar el Desempeño de React](https://www.toptal.com/react/componentes-react-eficientes-una-guia-para-optimizar-el-desempeno-de-react)
- [Top 9: Las Mejores técnicas de optimización para aplicaciones React](https://ourcodeworld.co/articulos/leer/934/top-9-las-mejores-tecnicas-de-optimizacion-para-aplicaciones-react)
