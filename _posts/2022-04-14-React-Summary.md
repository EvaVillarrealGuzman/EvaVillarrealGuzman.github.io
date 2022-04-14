---
title: React Summary
published: true
---

This is a summary about general concepts of React. Most of this is based from two learning paths of the Educative courses, [React Development for Professionals](https://www.educative.io/path/react-development-for-professionals) and [React for Front-End Developers](https://www.educative.io/path/react-front-end-developer), and the book [React 17 Design Patterns and Best Practices](https://www.amazon.com/React-Design-Patterns-Practices-industry-standard/dp/1800560443). I highly recommend this resources to improve your skills in React.


# [](#header-1) Differentiating between declarative and imperative programming

One of the reasons why React is so powerful is that it enforces a declarative programming paradigm.

Therefore, to master React, it is essential to understand what declarative programming means and what are the main differences between imperative and declarative programming. The easiest way to approach this is to think about imperative programming as a way of describing how things work, and declarative programming as a way of describing what you want to achieve.

Let's move into a JavaScript example. Here we will write a simple function that, given an array of lowercase strings, returns an array with the same strings in uppercase:

```javascript
toUpperCase(['foo', 'bar']) // ['FOO', 'BAR']
An imperative function to solve the problem would be implemented as follows:

const toUpperCase = input => { 
  const output = []
   
  for (let i = 0; i < input.length; i++) { 
    output.push(input[i].toUpperCase())
  } 
    
  return output
}
```

First of all, an empty array to contain the result is created. Then, the function loops through all the elements of the input array and pushes the uppercase values into the empty array. Finally, the output array is returned.

A declarative solution would be as follows:

```javascript
const toUpperCase = input => input.map(value => value.toUpperCase())
```

The items of the input array are passed to a map function that returns a new array containing the uppercase values. There are some significant differences to note: the former example is less elegant and it requires more effort to be understood. The latter is terser and easier to read, which makes a huge difference in big code bases, where maintainability is crucial.

Another aspect worth mentioning is that in the declarative example, there is no need to use variables, nor to keep their values updated during the execution. **Declarative programming tends to avoid creating and mutating a state**.

As a final example, let's see what it means for React to be declarative. The problem we will try to solve is a common task in web development: creating a toggle button.

Imagine a simple UI component such as a toggle button. When you click it, it turns green (on) if it was previously gray (off), and switches to gray (off) if it was previously green (on).

The imperative way of doing this would be as follows:

```javascript
const toggleButton = document.querySelector('#toggle')

toogleButton.addEventListener('click', () => {
  if (toggleButton.classList.contains('on')) {
    toggleButton.classList.remove('on')
    toggleButton.classList.add('off')
  } else {
    toggleButton.classList.remove('off')
    toggleButton.classList.add('on')
  }
})
```

It is imperative because of all the instructions needed to change the classes. In contrast, the declarative approach using React would be as follows:

```react
// To turn on the Toggle
<Toggle on />

// To turn off the toggle
<Toggle />
```

In declarative programming, developers only describe what they want to achieve, and there's no need to list all the steps to make it work. The fact that React offers a declarative approach makes it easy to use, and consequently, the resulting code is simple, which often leads to fewer bugs and more maintainability.

# [](#header-1) Functional programming

FP is a declarative paradigm, where side effects are avoided and data is considered immutable to make the code easier to maintain and reason about.

Don't consider the following sub-sections as an exhaustive guide to FP; it is only an introduction to get started with some concepts that are commonly used in React of which you should be aware.

## [](#header-2)  First-class functions

JavaScript has first-class functions because they are treated like any other variable, meaning you can pass a function as a parameter to other functions, or it can be returned by another function and be assigned as a value to a variable.

This allows us to introduce the concept of **Higher-Order Functions** (HoFs). HoFs are functions that take a function as a parameter, and optionally some other parameters, and return a function. The returned function is usually enhanced with some special behaviors.

Let's look at an example:

```javascript
const add = (x, y) => x + y

const log = fn => (...args) => { 
  return fn(...args)
}

const logAdd = log(add)
```

Here, a function is adding two numbers that enhance a function that logs all the parameters and then executes the original function.

This concept is pretty important to understand because in the React world, a common pattern is to use HOCs to treat our components as functions, and to enhance them with common behaviors.

## [](#header-2) Purity

An important aspect of FP is to write pure functions. You will encounter this concept very often in the React ecosystem, especially if you look into libraries such as Redux.

A function is pure when there are no side effects, which means that **the function does not change anything that is not local to the function itself**.

For example, a function that changes the state of an application, or modifies variables defined in the upper scope, or a function that touches external entities, such as the Document Object Model (DOM), is considered impure. Impure functions are harder to debug, and most of the time it is not possible to apply them multiple times and expect to get the same result.

For example, the following function is pure:

```javascript
const add = (x, y) => x + y
```

It can be run multiple times, always getting the same result, because nothing is stored anywhere and nothing gets modified.

The following function is not pure:

```javascript
let x = 0
const add = y => (x = x + y)
```

Running add(1) twice, we get two different results. The first time we get 1, but the second time we get 2, even if we call the same function with the same parameter. The reason we get that behavior is that the global state gets modified after every execution.

## [](#header-2) Immutability

We have seen how to write pure functions that don't mutate the state, but what if we need to change the value of a variable? In FP, a function, instead of changing the value of a variable, creates a new variable with a new value and returns it. This way of working with data is called immutability.

An immutable value is a value that cannot be changed.

Let's look at an example:

```javascript
const add3 = arr => arr.push(3)
const myArr = [1, 2]

add3(myArr); // [1, 2, 3]
add3(myArr); // [1, 2, 3, 3]
```

The preceding function doesn't follow immutability because it changes the value of the given array. Again, if we call the same function twice, we get different results.

We can change the preceding function to make it immutable using concat, which returns a new array without modifying the given one:

```javascript
const add3 = arr => arr.concat(3)
const myArr = [1, 2]
const result1 = add3(myArr) // [1, 2, 3]
const result2 = add3(myArr) // [1, 2, 3]
```

After we have run the function twice, myArr still has its original value.

## [](#header-2) Currying

A common technique in FP is currying. Currying is **the process of converting a function that takes multiple arguments into a function one argument at a time and returning another function**. Let's look at an example to clarify the concept.

Let's start with the add function we have seen before and transform it into a curried function.

Say we have the following code:

```javascript
const add = (x, y) => x + y
```

We can instead define the function as follows:

```javascript
const add = x => y => x + y
```

We use it in the following way:

```javascript
const add1 = add(1)
add1(2); // 3
add1(3); // 4
```

This is a pretty convenient way of writing functions because, since the first value is stored after the application of the first parameter, we can reuse the second function multiple times.

## [](#header-2) Composition

Finally, an important concept in FP that can be applied to React is composition. Functions (and components) can be combined to produce new functions with more advanced features and properties.

Consider the following functions:

```javascript
const add = (x, y) => x + y
const square = x => x * x
```

These functions can be composed together to create a new function that adds two numbers and then doubles the result:

```javascript
const addAndSquare = (x, y) => square(add(x, y))
```

Following this paradigm, we end up with small, simple, testable pure functions that can be composed together.

## [](#header-2) FP and UIs

The last step is to learn how we can use FP to build UIs, which is what we use React for.

We can think about a UI as a function to which the state of the application is applied as follows:

```javascript
UI = f(state)
```

We expect this function to be idempotent so that **it returns the same UI given the same state of the application**.


# [](#header-1) How React elements work

Whenever you call createClass, extend Component, or declare a stateless function, you are creating a component. React manages all the instances of your components at runtime, and there can be more than one instance of the same component in memory at a given point in time.

To control the UI flow, React uses a particular type of object, called an element, which describes what has to be shown on the screen. These immutable objects are much simpler compared to the components and their instances and contain only the information that is strictly needed to represent the interface.

The following is an example of an element:

```javascript
  { 
    type: Title, 
    props: { 
      color: 'red', 
      children: 'Hello, Title!' 
    } 
  }
```

Elements have **type**, which is the most important attribute, and some **properties**. There is also a particular property, called **children**, that is optional and represents the direct descendant of the element.

type is important because it tells React how to deal with the element itself. **If type is a string, the element represents a DOM node, while if type is a function, the element is a component**.

DOM elements and components can be nested with each other as follows, to represent the render tree:

```javascript
  { 
    type: Title, 
    props: { 
      color: 'red', 
      children: { 
        type: 'h1', 
        props: { 
          children: 'Hello, H1!' 
        } 
      } 
    } 
  }
```

When the type of the element is a function, React calls the function, passing props to get back the underlying elements. It keeps on performing the same operation recursively on the result until it gets a tree of DOM nodes that React can render on the screen. This process is called **reconciliation**, and it is used by both React DOM and React Native to create the UIs of their respective platforms.

# [](#header-1) Unlearning

Using React for the first time usually requires an open mind because it is a new way of designing web and mobile applications. React tries to innovate the way we build UIs following a path that breaks most of the well-known best practices.

In the last two decades, we learned that the **separation of concerns** is important, and we used to think about it as separating the logic from the templates. Our goal has always been to write the JavaScript and the HTML in different files. Various templating solutions have been created to help developers achieve this.

The problem is that most of the time, that kind of separation is just an illusion and the truth is that the JavaScript and the HTML are tightly coupled, no matter where they live.

React puts the templates where they belong – next to the logic. The reason it does that is that React suggests you organize your applications by composing small bricks called components. The framework should not tell you how to separate the concerns because every application has its own, and only the developers should decide how to limit the boundaries of their applications.

The component-based approach drastically changes the way we write web applications, which is why the classic concept of separation of concerns is gradually being taken over by a much more modern structure. The paradigm enforced by React is not new, and it was not invented by its creators, but React has contributed to making the concept mainstream and, most importantly, popularized it in such a way that it is easier to understand for developers with different levels of expertise.

Rendering of a React component looks like this:

```react
return ( 
  <button style={{ color: 'red' }} onClick={this.handleClick}> 
    Click me! 
  </button> 
)
```

Using JavaScript for both logic and templating not only helps us separate our concerns in a better way, but it also gives us more power and more expressivity, which is what we need to build complex UIs.

There is another concept that is pretty controversial and hard to accept, and that the engineers behind React are trying to push to the community: moving the styling logic inside the component, too. The end goal is to encapsulate every single technology used to create our components and separate the concerns according to their domain and functionalities.

Here is an example of a style object taken from the React documentation:

```react
const divStyle = { 
  color: 'white', 
  backgroundImage: `url(${imgUrl})`, 
  WebkitTransition: 'all', // note the capital 'W' here 
  msTransition: 'all' // 'ms' is the only lowercase vendor prefix 
}
   
ReactDOM.render(<div style={divStyle}>Hello World!</div>, mountNode)
```

This set of solutions, where developers use JavaScript to write their styles, is known as **#CSSinJS**.

# [](#header-1)Understanding JavaScript fatigue

There is a prevailing opinion that React consists of a vast set of technologies and tools, and if you want to use it, you are forced to deal with package managers, transpilers, module bundlers, and an infinite list of different libraries. This idea is so widespread and shared among people that it has been clearly defined, and has been given the name JavaScript fatigue.

It is not hard to understand the reasons behind this. All the repositories and libraries in the React ecosystem are made using shiny new technologies, the latest version of JavaScript, and the most advanced techniques and paradigms.

Moreover, there is a massive number of React boilerplate on GitHub, each with tens of dependencies to offer solutions for any problems. It is straightforward to think that all these tools are required to start using React, but this is far from the truth. Despite this common way of thinking, React is a pretty tiny library, and it can be used inside any page (or even inside JSFiddle) in the same way everyone used to use jQuery or Backbone, just by including the script on the page before the closing body element.

There are two scripts because React is split into two packages:

- react: Implements the core features of the library
- react-dom: Contains all the browser-related features

The reason behind this is that the core package is used to support different targets, such as React DOM in browsers and React Native on mobile devices. Running a React application inside a single HTML page does not require any package manager or complex operation. You can just download the distribution bundle and host it yourself (or use https://unpkg.com/), and you are ready to get started with React and its features in a few minutes.

If we add the core React library only, we cannot use JSX because it is not a standard language supported by the browser; but the whole point is to start with the bare minimum set of features and add more functionalities as soon as they are needed. For a simple UI, we could just use createElement (_jsx on React 17) and only when we start building something more complex can we include a transpiler to enable JSX and convert it into JavaScript. As soon as the app grows a bit more, we may need a router to handle different pages and views, and we can include that as well.

At some point, we may want to load data from some API endpoints, and if the application keeps growing, we will reach the point where we need some external dependencies to abstract complex operations. Only at that very moment should we introduce a package manager. Then, the time will come to split our application into separate modules and organize our files in the right way. At that point, we should start thinking about using a module bundler.

Following this simple approach, there's no fatigue. Starting with a boilerplate that has 100 dependencies and tens of npm packages of which we know nothing is the best way to get lost. It is important to note that every programming-related job (and frontend engineering in particular) requires continuous learning. It is the nature of the web to evolve at a breakneck pace and change according to the needs of both users and developers. This is the way our environment has worked since the beginning and is what makes it very exciting.

As we gain experience working on the web, we learn that we cannot master everything and we should find the right way to keep ourselves updated to avoid fatigue. We are able to follow all the new trends without jumping into the new libraries for the sake of it unless we have time for a side project.

It is astonishing how, in the JavaScript world, as soon as a specification is announced or drafted, someone in the community implements it as a transpiler plugin or a polyfill, letting everyone else play with it while the browser vendors agree and start supporting it.

This is something that makes JavaScript and the browser a completely different environment compared to any other language or platform. The downside of it is that things change quickly, but it is just a matter of finding the right balance between betting on new technologies versus staying safe.

In any case, Facebook developers care a lot about the **Developer Experience** (DX), and they listen carefully to the community. So, even if it is not true that to use React we are required to learn hundreds of different tools, they realized that people were feeling the fatigue and they released a CLI tool that makes it incredibly easy to scaffold and run a real React application.

The only requirement is to use a node.js/npm environment and install the CLI tool globally as follows:

```sh
npm install -g create-react-app
```

When the executable is installed, we can use it to create our application, passing a folder name:

```sh
create-react-app hello-world --template typescript
```

Finally, we move into the folder of our application with ```cd hello-world```, and we just run the following command:

```sh
npm start
```

Magically, our application is running with a single dependency, but with all the features needed to build a complete React application using the most advanced techniques.

# [](#header-1) Using JSX

React provides two ways to define our elements. The first one is by using JavaScript functions, and the second one is by using JSX, an optional XML-like syntax.

 it is similar to HTML and looks very familiar to anyone who has already created UIs on the web. The opening and closing tags make it easier to represent nested trees of elements, something that would have been unreadable and hard to maintain using plain JavaScript.

## [](#header-2) Babel 7

To use JSX (and some features of ES6) in our code, we have to install the new Babel 7. Babel is a popular JavaScript compiler widely adopted within the React community.

First of all, it is important to clearly understand the problems it can solve for us and why we need to add a step to our process. **The reason is that we want to use features of the language that have not yet been added in the browser, our target environment. Those advanced features make our code cleaner for developers, but the browser cannot understand and execute it**.

The solution is to write our scripts in JSX and ES6 and, when we are ready to ship, we compile the sources into ES5, the standard specification implemented in major browsers today.

Babel can compile ES6 code into ES5 JavaScript, as well as compile JSX into JavaScript functions. This process is called **transpilation** because **it compiles the source into a new source rather than into an executable**.

To install Babel, we need to install @babel/core and @babel/node as follows:

```sh
npm install -g @babel/core @babel/node
```

One of the reasons why Babel is so powerful is because it is **highly configurable**. Babel is just a tool to transpile a source file into an output file, but to apply some transformations, we need to configure it.

Luckily, **there are some very useful presets of configurations that we can easily install and use**:

npm install -g @babel/preset-env @babel/preset-react
Once the installation is complete, we create a configuration file called .babelrc in the root folder, and put the following lines into it to tell Babel to use those presets:

```json
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-react"
  ]
}
```

## [](#header-2) DOM elements and React components

With JSX, we can create both HTML elements and React components; the only difference is whether or not they start with a capital letter.

For example, to render an HTML button, we use <button />, while to render the Button component, we use <Button />. The first button is transpiled into the following:

```
_jsx('button', {})
```

The second one is transpiled into the following:

```
_jsx(Button, {})
```

The difference here is that in the first call, we are passing the type of the DOM element as a string, while in the second call, we are passing the component itself, which means that it should exist in the scope to work.

As you may have noticed, JSX supports self-closing tags, which are pretty good for keeping the code terse and do not require us to repeat unnecessary tags.

JSX is only **syntactic sugar**, and it gets transpiled into JavaScript before being executed in the browser. In fact, our <button /> element is translated into _jsx('button', {}) when we run Babel, which is something we should always keep in mind when we write our templates.

## [](#header-2) Props

JSX is very convenient when your DOM elements or React components have props. Using XML is pretty easy to set attributes on elements:

```html
<img src="https://www.js.education/images/logo.png" alt="JS Education" />
```

The equivalent in JavaScript would be as follows:

```javascript
_jsx("img", { 
  src: "https://www.js.education/images/logo.png", 
  alt: "JS Education" 
})
```

## [](#header-2) Children

JSX allows you to define children to describe the tree of elements and compose complex UIs. A basic example is a link with text inside it, as follows:

```html
<a href="https://js.education">Click me!</a>
```

This would be transpiled into the following:

```javascript
_jsx( 
  "a", 
  { href: "https://www.js.education" }, 
  "Click me!" 
)
```

Our link can be enclosed inside a div element for some layout requirements, and the JSX snippet to achieve that is as follows:

```html
<div> 
  <a href="https://www.js.education">Click me!</a> 
</div>
```

The JavaScript equivalent is as follows:

```javascript
_jsx( 
  "div", 
  null, 
  _jsx( 
    "a", 
    { href: "https://www.js.education" }, 
    "Click me!" 
  ) 
)
```

It should now be clear how the XML-like syntax of JSX makes everything more readable and maintainable, but it is always important to know the JavaScript parallel to our JSX has control over the creation of elements. The good part is that we are not limited to having elements as children of elements, but we can use JavaScript expressions, such as functions or variables.

To do this, we have to enclose the expression within curly braces:

```html
<div> 
  Hello, {variable}. 
  I'm a {() => console.log('Function')}. 
</div> 
```

The same applies to non-string attributes, as follows:

```html
<a href={this.createLink()}>Click me!</a>
```

As you see, any variable or function should be enclosed with curly braces.

## [](#header-2) Differences with HTML

So far, we have looked at the similarities between JSX and HTML. Let's now look at the little differences between them and the reasons they exist.

### [](#header-3) Attributes

We must always keep in mind that JSX is not a standard language and that it gets transpiled into JavaScript. Because of this, some attributes cannot be used.

For example, instead of class, we have to use className, and instead of for, we have to use htmlFor, as follows:

```react
<label className="awesome-label" htmlFor="name" />
```

The reason for this is that class and for are reserved words in JavaScript.

### [](#header-3) Style

A pretty significant difference is the way the style attribute works. The style attribute does not accept a CSS string as the HTML parallel does, but it expects a JavaScript object where the style names are camelCased:

```react
<div style={{ backgroundColor: 'red' }} />
```

As you can see, you can pass an object to the style prop, meaning you can even have your styles in a separate variable if you want:

```react
const styles = {
  backgroundColor: 'red'
} 

<div style={styles} />
```

This is the best way to have better control of your inline styles.

### [](#header-3) Root

One important difference with HTML worth mentioning is that since JSX elements get translated into JavaScript functions, and you cannot return two functions in JavaScript, whenever you have multiple elements at the same level, you are forced to wrap them in a parent.

Let's look at a simple example:

```react
<div />
<div />
```

This gives us the following error:

```
Adjacent JSX elements must be wrapped in an enclosing tag.
```

On the other hand, the following works:

```react
<div> 
  <div /> 
  <div /> 
</div>
```

Before, React forced you to return an element wrapped with an <div> element or any other tag; since React 16.2.0, it is possible to return an array directly as follows:

```react
return [
  <li key="1">First item</li>, 
  <li key="2">Second item</li>, 
  <li key="3">Third item</li>
]
```

Or you can even return a string directly, as shown in the following code block:

```react
return 'Hello World!'
```

Also, React now has a new feature called **Fragment** that also works as a special wrapper for elements. It can be specified with React.Fragment:

```react
import { Fragment } from 'react'

return ( 
  <Fragment>
    <h1>An h1 heading</h1> 
    Some text here. 
    <h2>An h2 heading</h2> 
    More text here.
    Even more text here.
  </Fragment>
)
```

Or you can use empty tags (<></>):

```react
return ( 
  <>
    <ComponentA />
    <ComponentB />
    <ComponentC />
  </>
)
```

Fragment won't render anything visible on the DOM; it is just a helper tag to wrap your React elements or components.

### [](#header-3) Spaces

There's one thing that could be a little bit tricky in the beginning and, again, it concerns the fact that we should always keep in mind that JSX is not HTML, even if it has XML-like syntax. JSX handles the spaces between text and elements differently from HTML, in a way that's counter-intuitive.

Consider the following snippet:

```react
<div> 
  <span>My</span> 
  name is 
  <span>Carlos</span> 
</div>
```

In a browser that interprets HTML, this code would give you My name is Carlos, which is exactly what we expect.

In JSX, the same code would be rendered as MynameisCarlos, which is because the three nested lines get transpiled as individual children of the div element, without taking the spaces into account. A common solution to get the same output is putting a space explicitly between the elements, as follows:

```react
<div> 
  <span>My</span> 
  {' '}
  name is
  {' '} 
  <span>Carlos</span> 
</div>
```

As you may have noticed, we are using an empty string wrapped inside a JavaScript expression to force the compiler to apply a space between the elements.

### [](#header-3) Boolean attributes

A couple more things are worth mentioning before really starting regarding the way you define Boolean attributes in JSX. If you set an attribute without a value, JSX assumes that its value is true, following the same behavior as the HTML disabled attribute, for example.

This means that if we want to set an attribute to false, we have to declare it explicitly as false:

```react
<button disabled /> 
React.createElement("button", { disabled: true })
```

The following is another example of the Boolean attribute:

```react
<button disabled={false} /> 
React.createElement("button", { disabled: false })
```

This can be confusing in the beginning, because we may think that omitting an attribute would mean false, but it is not like that. With React, we should always be explicit to avoid confusion.

## [](#header-2) Spread attributes

An important feature is the **spread attribute** operator (...), which comes from the rest/spread properties for ECMAScript proposal, and is very convenient whenever we want to pass all the attributes of a JavaScript object to an element.

A common practice that leads to fewer bugs is not to pass entire JavaScript objects down to children by reference, but to use their primitive values, which can be easily validated, making components more robust and error-proof.

Let's see how it works:

```react
const attrs = { 
  id: 'myId',
  className: 'myClass'
}
   
return <div {...attrs} />
```

The preceding code gets transpiled into the following:

```react
var attrs = { 
  id: 'myId',
  className: 'myClass'
} 

return _jsx('div', attrs)
```

## [](#header-2) Template literals

Templates literals are string literals allowing embedded expressions. You can use multiline strings and string interpolation features with them.

Template literals are enclosed by the backtick (` `) character instead of double or single quotes. Also, template literals can contain placeholders. You can add them using the dollar sign and curly braces (${expression}):

```react
const name = `Carlos`
const multilineHtml = `<p>
 This is a multiline string
 </p>`
console.log(`Hi, my name is ${name}`)
```

# [](#header-1) Why learn React?

- **Innovation**: React can be integrated seamlessly with any other library or framework because React is a view only library, (it is the view part of the Model View Controler MVC architectural UI pattern). The freedom to use any other framework and library makes it possible to experiment and so to innovate.
- **Simple API**: React has a relatively simple API and so has no steep learning curve. It only has a handful of methods to understand. The code to make ‘components’ of the UI (i.e., buttons etc) is straight-forward as there is very little boilerplate code which makes the library comparatively accessible.
- **JavaScript**: JavaScript is arguably the world’s most popular programming language and is also incredibly powerful. JavaScript is both a functional and an object-oriented programming language and so harnesses the advantages brought by both paradigms. React is in JavaScript and so inherits its benefits.
- **ES6**: “ES6 refers to version 6 of the ECMA Script programming language. ECMA Script is the standardized name for JavaScript, and version 6 is the next version after version 5, which was released in 2011. It is a major enhancement to the JavaScript language, and adds many more features intended to make large-scale software development easier.” You can read more for the details of the new features that ES6 introduces.
- **JSX**: JSX stands for Javascript Syntax Extension. It allows you to use HTML and XML in JavaScript. Essentially, the HTML/XML code is ‘transpiled’ into JavaScript. In other words, the HTML/XML is converted into JavaScript before the code is compiled. This enables the full power of JavaScript to be employed with the relative ease of writing in HTML.
- **Unidirectional Data Flow**: React implements one-way data flow which makes it easy to reason about your app. Essentially all the parts of the UI are contained within each other which makes tracing data flow super simple.
- **Community**: React is a very popular and growing library. This means that the React community is large and growing too. So there are a plethora of resources out there to help you as you learn. Essentially, you won’t get ‘stuck’ anywhere for extended periods of time as someone will have had the same problem as you before and is likely to have posted about it online.


# [](#header-1)Functional React component vs Class React component

**Functional React component**

- Functional React components are the simplest React components. Functional React components do not hold an internal state. They are often referred to as “dumb” components.
- Functional components are “pure functions” that accept props as input and return UI code in the form of JSX.

**Class React component**

- Class components allow you to create stateful logic in React components. It also allows you to use lifecycle methods for side effects. 
- Until React 16.8, developers always needed ES6 classes to maintain the internal state in React components and to properly utilize lifecycle methods. With React Hooks, this will change.

# [](#header-1)Introduction to React Hooks

Many engineers and developers love the functional programming principles that React promotes. Certain concepts like pure functions, props, state, and stateful logic allow developers to write feature rich web applications. This works very well except for one caveat.

React needed improvement in one area: sharing stateful logic in React components. Higher order components partially solves this issue. But, higher code components do not achieve the best code composition, code reuse, and unit testing ability.

This is where React Hooks come in handy. With React Hooks, there is the freedom to create and share stateful logic in React components. It is even possible to share such hooks publicly as “npm” (node package manager) packages.

These are called “React Hooks” because they allow developers to hook into React features that were previously unexposed publicly.

React Hooks API is available in React 16.8+. **React Hooks allow you to create stateful logic in functional React components**.

React Hooks are backward-compatible, which means it does not contain any breaking change and it does not replace your knowledge of React concepts.

## [](#header-2) Benefits of Hooks

- **Better code composition**: React Hooks allow you to write code in a more functional way. When using classes, the side effects logic in different lifecycle events are being divided. This makes it harder to follow code composition and the flow of data. Hooks solve this problem by allowing you to write side effects logic in a linear and rendering flow.
- **Solving HOC Hell**: With HOC (High-Order Components), wrap one component in another function and return a new component from it. Depending upon the complexity of a component, it may require several HOCs to create fully functional logic. With React Hooks, simply “use” any hook in a functional component but avoid running into the trap of wrapping components.
- **Better code reuse**: React Hooks make it easier to share stateful logic between different components. 
- **Better testing**: React Hooks are JavaScript functions that make it easier to test the stateful logic defined in it. It can also be tested independently of other functionalities. You can easily mock initial props for a hook and write unit tests to compare the output from the hook.
- **Performance**: It is no secret that functional components are usually faster than classes in React. However, the distinction is not clear when comparing React Hooks with classes. If used properly, React Hooks can be faster than classes or faster when using HOCs.

## [](#header-2) Rules of Hooks

React requires you to follow some rules when calling React Hooks. These rules are important for React Hooks to function as expected. If one of the rules is not adhered to, React library will throw a runtime error known as ```Invalid Hook Call Warning```.

### [](#header-3) Rule 1: Only call hooks at the top level

Hooks should only be called at the top level of a component.

Hooks should not be called in if statements, loops, or nested functions. React Hooks such as ```useState``` and ```useEffect``` rely on the order in which they are called to preserve the current context and to calculate for the next state or side effect accordingly.

If the order of hook calls is different in each render, it would break this flow in React.

### [](#header-3) Rule 2: Only call hooks from React functions

The second rule of React Hooks says that you should only call React Hooks from a React functional component and custom Hooks.


## [](#header-2) React Hooks API

### [](#header-3) useState Hook

React Props are used to pass information down the component tree; React state is used to make applications interactive. We’ll be able to change the application’s appearance by interacting with it. Here is a utility function called ```useState``` that we take from React for managing state.

The ```useState``` hook is an alternative to using ```this.state``` or ```this.setState``` in class components as managing state with React Hooks.

Anytime a functional component needs to hold the internal state, use the ```useState``` hook to remember the state. In other words, **create stateful logic by using the ```useState``` hook**.

Each time you call the ```useState``` hook, React creates an entirely new state in memory and returns the reference to that state.

#### [](#header-4) Syntax

```react
const [state, setState] = useState(INITIAL_STATE);
```

The useState hook only takes one argument which is the value of the initial state.

It returns an array with two items in it:

1- The first item in this array is a variable that holds the current value of the state.
2- The second item is a function for updating the state variable with the new value.

### [](#header-3) useEffect Hook

The ```useEffect``` hook provides a way to manage side effects in a React functional component. Think of the ```useEffect``` hook as a partial replacement for React lifecycle events. With the ```useEffect``` hook, it is possible to replicate behavior for componentDidMount, componentDidUpdate and componentWillUnmount methods.

In other words, you can react upon changes happening in a component that is using the ```useEffect``` hook. 

Some common scenarios to use the ```useEffect``` hook with are mentioned below:

- Add an event listener for a button
- Fetch data from API when component mounts
- Perform an action when state or props change
- Clean up event listeners when the component unmounts

#### [](#header-4) Syntax

```useEffect``` hook takes the two arguments mentioned below:

1- It accepts the first argument as a callback function. By default, this function will run after every render but this depends upon the value of the second argument.
2- The second argument is an optional **dependencies array**. ```useEffect``` hook checks this array to compare the previous and current value for those dependencies and then it runs the callback function but only if one of the dependencies has changed.

```react
 useEffect(() => {
    // some code
  }, [someProp, someState]);
```

**Dependencies array** is a way to tell ```useEffect``` hook that our effect only depends on props provided in this array. Do not worry about anything else available in the component scope.

When adding an empty array as the dependency array argument, the effect will run once and only after the first render. Because an empty array never changes, useEffect will skip running it on subsequent renders. In short, a componentDidMount method is created by using the ```useEffect``` hook:

```react
useEffect(() => {
  loadMessage();
}, []);
```

### [](#header-3) useContext Hook

```useContext``` provides the current context value of the context object passed to it. The ```useContext``` hook relies on nearest Context Provider to determine the current value for that context.

A lot of people are moving away from Redux in order to use the new Context API. Context provides a way to share data between components without passing a prop to all the child components.

Assume you have an application with different theme colors depending upon the brand definition.

```react
import React, { createContext, useContext } from 'react';
const ThemeContext = createContext('green');

const App = () => {
  return (
    <ThemeContext.Provider value={'green'}>
      <Content />
    </ThemeContext.Provider>
  );
};

const Content = () => {
  const theme = useContext(ThemeContext);
  return <Button theme={theme} />;
};
```

First, create a context object ```ThemeContext``` using ```createContext``` method from React library. ```ThemeContext``` has the default theme value as green.

Later, wrap the app with ```ThemeContext.Provider``` as well as specify the theme value.

Call ```useContext``` to return the current value of ```ThemeContext```.

The ```useContext``` hook is used to retrieve the value of a given context.

#### [](#header-4) Syntax

```useContext``` accepts a React Context object as the only input.

The output of ```useContext``` is the current value of the provided context.

```react
const value = useContext(MyThemeContext);
```

This value is determined by the nearest Context Provider. If the value of Provider changes, ```useContext``` hook will trigger a re-render in the calling component.

> If a parent component uses memoization to skip renders, the ```useContext``` hook will still cause a re-render in a component where it was called in case context value changes.

### [](#header-3) useReducer Hook

```useReducer``` hook is an alternative to ```useState```. It offers more control on the next state value based on a given action.

```useReducer``` can be used in the following scenarios:

- The shape of state object is complex. Maintain multiple items within the same state object.
- Access previous state value to update the next state.
- Apply special logic on certain actions to calculate the next state.

Note that some of these points can be achieved by using ```useState``` hook. However, it would require maintaining more code in the shape of various functions.

Instead of setting state explicitly with the state updater function from ```useState```, the ```useReducer``` state updater function dispatches an action for the reducer It’s valid to move this logic into the reducer function and manage the reducer with an action, which is another case for moving from imperative to declarative programming. Instead of doing it ourselves by saying how it should be done, we are telling the reducer what to do. Everything else is hidden in the reducer.

```useReducer``` Hook is pretty much the same as redux in terms of the dispatch method, actions, and reducers, but the main difference is that this is limited just to the context of your component and its child, so if you need a global store to be accessible from your entire application then you should use react-redux instead (or use React Context API).

#### [](#header-4) Syntax

The ```useReducer``` hook accepts three arguments:

- **Reducer**: This is the reducer function. When an action is dispatched, it is called with the current state and the action as first and second argumen- respectively.
- **Initial State**: Supply the value of the initial state here.
- **Init Callback**: This parameter is optional. You should only supply it when looking to initialize a state which is based on a custom value or prop at - later stage. When provided, React will call this function as ```initCb(initialState)``` to obtain the initial state for the ```useReducer``` hook.

```react
const [state, dispatch] = useReducer(reducer, initialState, initCb);
```

It returns an array with state value and dispatch function.

The dispatch function is similar to the ```setState``` function seen in ```useState``` hook. However, it is a special use case function. When ```dispatch``` is called, React will automatically call the reducer with the action provided to the ```dispatch``` function.

#### [](#header-4) Imperative React

In a React function component, React’s ```useRef``` Hook is used mostly for imperative programming. Throughout React’s history, the ref and its usage had a few versions:

- String Refs (deprecated)
- Callback Refs
- createRef Refs (exclusive for Class Components)
- useRef Hook Refs (exclusive for Function Components)


> In general, we should try to avoid using refs because they force the code to be more imperative, and they become harder to read and maintain.

### [](#header-3) Memoization Hooks

Memoization is an optimization technique to store the result of an expensive calculation for a given input. The cached result is returned for the subsequent function calls as long as the same input occurs again.

React API provides two hooks to provide the memoization feature for expensive calculations:
- useCallback
- useMemo

Both ```useCallback``` and ```useMemo``` provide similar functionality but with one main difference mentioned below:

- Use the ```useCallback``` hook when wanting to memoize the callback function. This callback may perform several actions when invoked.
- Use ```useMemo``` hook when wanting to memoize output returned from an expensive compute function. This compute function may perform heavy calculations on each function call.

Use memoization hooks only when there are benefits in terms of rendering performance. For example, when rendering large lists, a memoized result may speed up the actual rendering performance.

For simple cases, it is not required to use memoization because the overhead may not offset the performance improvement.

#### [](#header-4) Syntax

```useCallback``` hook accepts two arguments: An inline callback function and an array of dependencies. ```useCallback``` returns a memoized reference to the callback function when the dependencies do not change.

```react
const memoizedCallback = useCallback(
  () => {
    calculateFn(input);
  },
  [input],
);
```

Similarly, ```useMemo``` hook accepts two arguments: A callback that returns the result of an expensive compute function and an array of dependencies. ```useMemo``` returns the last cached result if the dependencies do not change.

```react
const memoizedValue = useMemo(() => getExpensiveCalculationResult(input), [input]);
```

#### [](#header-4) Memoizing a component with memo

The memo High Order Component (HOC) is similar to PureComponent of a React class because it performs a shallow comparison of the props (meaning a superficial check), so if we try to render a component with the same props all the time, the component will render just once and will memorize. The only way to re-render the component is when a prop changes its value.

It is not a good idea to add memo to all our components unless it is totally necessary, otherwise, the process of shallow comparisons and memorization will have inferior performance than if we don't use it. Normally, when we have small components or basic logic, we don't need this unless you're working with large data from some API or your component needs to perform a lot of renders (normally huge lists), or when you notice that your app is going slow. Only in that case would I recommend using memo.

#### [](#header-4) Summary

**memo:**
- Memorizes a component
- Re-memorizes when props change
- Avoids re-renders
- Memo is not a hooks, this is a HOC

**useMemo:**
- Memorizes a calculated value
- For computed properties
- For heavy processes

**useCallback:**
- Memorizes a function definition to avoid redefining it on each render.
- Use it whenever a function is passed as an effect argument.
- Use it whenever a function is passed by props to a memorized component.
  
> Do not forget the golden rule: Do not use them until absolutely necessary.

### [](#header-3) useRef Hook

You can use ```useRef``` hook to get the ref of a DOM node. Later, you can use this ref for certain actions related to that node. For example, scrolling to an element position. Other use cases may require getting the height of a DOM node or focusing an input.

```useRef``` provides the ref object with the current property set to the reference of node in DOM.

### [](#header-3) useLayoutEffect Hook

```useLayoutEffect``` is an alternative to useEffect for layout updates.

A classic usage for this hook is to truncate long content if the length of the text is too long. ```useLayoutEffect``` synchronously re-renders UI before the browser can paint.

### [](#header-3) Create Your Own Hooks

If an application needs the same stateful logic in two different places, it is a good idea to extract this code into your own hook. By doing so, you can share any stateful logic as a standalone module that can be tested on its own, shared, and reused.

Custom hooks can take any number of input arguments and return anything just to suit your needs. In addition, you are not limited to returning an object, an array of objects, or even another function.

Building a custom hook helps share stateful logic between components. A custom hook can use its own dedicated state or use a global Store.

A custom hook can encapsulate non-trivial implementation details that should be kept away from a component; can be used in more than one React component, and can even be open-sourced as an external library. 

#### [](#header-4) Guidelines for creating custom hooks

- Hook name should start with the word “use”
- Rules of hooks still apply to custom hooks
- Custom hooks do not share state: When starting to use the same custom hook in different components, those components do not share state automatically. This is based on the original principles of React Hooks. 


## [](#header-2) React Suspense

React Suspense was introduced in React 16.6. Suspense lets you suspend component rendering until a condition is met. You can render a loading component or anything you want as a fallback of Suspense. Right now there are only two use cases for this:

- Code splitting: When you split your application and you're waiting to download a chunk of your app when a user wants to access it
- Data fetching: When you're fetching data

In both scenarios, you can render a fallback, which can normally be a loading spinner, some loading text, or even better, a placeholder skeleton.



# [](#header-1) Handling events

Events work in a slightly different way across various browsers. React tries to abstract the way events work and give developers a consistent interface to deal with. This is a great feature of React because we can forget about the browsers we are targeting and write event handlers and functions that are vendor-agnostic.

To offer this feature, React introduced the concept of the **synthetic event**. A synthetic event is an object that wraps the original event object provided by the browser, and it has the same properties, no matter where it is created.

To attach an event listener to a node and get the event object when the event is fired, we can use a simple convention that recalls the way events are attached to the DOM nodes. In fact, we can use the word on plus the camelCased event name (for example, onKeyDown) to define the callback to be fired when the events happen. **A popular convention is to name the event handler functions after the event name and prefix them using handle (for example, handleKeyDown)**.

 We are going to implement a simple button, and we start, as usual, by creating a component:

```react
const Button = () => {

}
  
export default Button
```

Then we define the event handler:

```react
const handleClick = (syntheticEvent) => { 
  console.log(syntheticEvent instanceof MouseEvent)
  console.log(syntheticEvent.nativeEvent instanceof MouseEvent)
}
```

As you can see here, we are doing a very simple thing: we just check the type of the event object we receive from React and the type of native event attached to it. We expect the first to return false and the second to return true.

**You should never need to access the original native event, but it is good to know you can do it if you need to.** Finally, we define the button with the onClick attribute to which we attach our event listener:

```react
return ( 
  <button onClick={handleClick}>Click me!</button> 
)
```

Now, suppose we want to attach a second handler to the button that listens to the double-click event. One solution would be to create a new separate handler and attach it to the button using the onDoubleClick attribute, as follows:

```react
<button 
  onClick={handleClick} 
  onDoubleClick={handleDoubleClick} 
> 
  Click me! 
</button>
```

Remember that we always aim to write less boilerplate and avoid duplicating code. For that reason, **a common practice is to write a single event handler for each component, which can trigger different actions according to the event type.**

Let's implement the generic event handler:

```react
const handleEvent = (event) => { 
  switch (event.type) { 
    case 'click': 
      console.log('clicked')
      break
   
    case 'dblclick': 
      console.log('double clicked')
      break
   
    default: 
      console.log('unhandled', event.type)
  } 
}
```

The generic event handler receives the event object and switches on the event type to fire the right action. This is particularly useful if we want to call a function on each event (for example, analytics) or if some events share the same logic.

Finally, we attach the new event listener to the onClick and onDoubleClick attributes:

```react
return ( 
  <button 
    onClick={handleEvent} 
    onDoubleClick={handleEvent} 
  > 
    Click me! 
  </button> 
) 
```

From this point on, whenever we need to create a new event handler for the same component, instead of creating a new method and binding it, we can just add a new case to the switch.

About events in React:
- This are that synthetic events are reused and that there is a single global handler. The first concept means that we cannot store a synthetic event and reuse it later because it becomes ```null``` right after the action. This technique is very good in terms of performance, but it can be problematic if we want to store the event inside the state of the component for some reason. To solve this problem, React gives us a ```persist``` method on the synthetic events, which we can call to make the event persistent so that we can store it and retrieve it later.
- Whenever we use the on attribute, we are describing to React the behavior we want to achieve, but the library does not attach the actual event handler to the underlying DOM nodes. What it does instead attaches a single event handler to the root element, which listens to all the events, thanks to **event bubbling**. When an event we are interested in is fired by the browser, React calls the handler on the specific components on its behalf. This technique is called **event delegation** and is used for memory and speed optimization.