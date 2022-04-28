---
layout: post
title: Better React code 
subtitle: Compilation of useful React conventions and techniques for building reusable, readable, and maintainable components.
categories: react
tags: [react]
--- 

This post is a compilation of my learnings in React, while I gained experience, that allowed me to write  code in a more maintainable, readable and reusable way. We'll look at React techniques and conventions, as well as anti-patterns, such as using indexes as key and spreading properties on DOM elements, which should be avoided.

## Multiline

Whenever we have nested elements, we should always go multiline:

```jsx
<div> 
  <Header /> 
  <div> 
    <Main content={...} /> 
  </div> 
</div>
```

This is preferable to the following:

```jsx
<div><Header /><div><Main content={...} /></div></div>
```

The exception is if the children are not elements such as text or variables. In that case, it makes sense to remain on the same line and avoid adding noise to the markup, as follows:

```jsx
<div> 
  <Alert>{message}</Alert> 
  <Button>Close</Button> 
</div>
```

Always remember to wrap your elements inside parentheses when you write them on multiple lines. JSX always gets replaced by functions, and functions written on a new line can give you an unexpected result because of automatic semicolon insertion. Suppose, for example, that you are returning JSX from your render method, which is how you create UIs in React.

The following example works fine because the div element is on the same line as the return:

```jsx
return <div />
```

The following, however, is not right:

```jsx
return 
  <div />
```

You can wrap the statement in parentheses, as follows:

```jsx
return ( 
  <div /> 
)
```

## Multi-properties

A common problem in writing JSX comes when an element has multiples attributes. One solution is to write all the attributes on the same line, but this would lead to very long lines that we do not want in our code.

A common solution is to write each attribute on a new line, with one level of indentation, and then align the closing bracket with the opening tag:

```jsx
<button 
  foo="bar" 
  veryLongPropertyName="baz" 
  onSomething={this.handleSomething} 
/>
```


## Conditionals

We use conditionals when, for example, we want to render some components only when certain conditions are matched. There are many different ways to express conditions in JSX, and it is important to understand the benefits and problems of each one of these to write code that is both readable and maintainable.

Suppose we want to show a logout button only if the user is currently logged in to our application.

A simple snippet to start with is as follows:

```jsx
let button
  
if (isLoggedIn) { 
  button = <LogoutButton />
} 
  
return <div>{button}</div>
```

This works, but it is not very readable, especially if there are multiple components and multiple conditions.

In JSX, we can use an inline condition:

```jsx
<div> 
  {isLoggedIn && <LoginButton />} 
</div>
```

This works because if the condition is false, nothing gets rendered, but if the condition is true, the createElement function of LoginButton gets called, and the element is returned to compose the resulting tree.

If the condition has an alternative (the classic if...else statement) and we want, for example, to show a logout button if the user is logged in and a login button otherwise, we can use JavaScript's if...else statement as follows:

```jsx
let button

if (isLoggedIn) { 
  button = <LogoutButton />
} else { 
  button = <LoginButton />
} 
  
return <div>{button}</div>
```

Alternatively, and better still, we can use a ternary condition that makes the code more compact:

```jsx
<div> 
  {isLoggedIn ? <LogoutButton /> : <LoginButton />} 
</div>
```

Let's now look at the best solution for when things get more complicated and, for example, we have to check more than one variable to determine whether to render a component or not:

```jsx
<div>
  {dataIsReady && (isAdmin || userHasPermissions) && 
    <SecretData />
  }
</div>
```

In this case, it is clear that using the inline condition is a good solution, but the readability is strongly impacted. Instead, we can create a helper function inside our component and use it in JSX to verify the condition:

```jsx
const canShowSecretData = () => { 
  const { dataIsReady, isAdmin, userHasPermissions } = props
  return dataIsReady && (isAdmin || userHasPermissions)
} 
  
return (
  <div> 
    {this.canShowSecretData() && <SecretData />} 
  </div>
)
```

As you can see, this change makes the code more readable and the condition more explicit.

The same applies to computed properties. Suppose you have two single properties for currency and value. Instead of creating the price string inside render, you can create a function:

```jsx
const getPrice = () => { 
  return `${props.currency}${props.value}`
}
   
return <div>{getPrice()}</div>
```

This is better because it is isolated and you can easily test it if it contains logic.

### External solutions

**A good practice is to avoid external dependencies as much as we can to keep our bundle smaller, but it may be worth it in this particular case because improving the readability of our templates is a big win.** We will see different external solutions:

#### render-if

We can install with the following:

```
npm install --save render-if
```

We can then easily use it in our projects, as follows:

```jsx
const { dataIsReady, isAdmin, userHasPermissions } = props

const canShowSecretData = renderIf( 
  dataIsReady && (isAdmin || userHasPermissions) 
);
   
return (
  <div> 
    {canShowSecretData(<SecretData />)} 
  </div> 
);
```

Here, we wrap our conditions inside the renderIf function.

The utility function that gets returned can be used as a function that receives the JSX markup to be shown when the condition is true.

One goal is to never add too much logic inside our components. Some of them will require a bit of it, but we should try to keep them as simple as possible so that we can easily spot and fix errors.

#### jsx-only-if

```jsx-only-if``` lets us write our components as if the condition is always true by setting the conditional function using a Higher-Order Component (HOC).

To use the library, we need to install it as follows:

```
npm install --save react-only-if
```

Once it is installed, we can use it in our apps in the following way:

```jsx
import onlyIf from 'react-only-if'

const SecretDataOnlyIf = onlyIf(
  ({ dataIsReady, isAdmin, userHasPermissions }) => dataIsReady && 
  (isAdmin || userHasPermissions)
)(SecretData)

const MyComponent = () => (
  <div>
    <SecretDataOnlyIf 
      dataIsReady={...}
      isAdmin={...}
      userHasPermissions={...}
    />
  </div>
)

export default MyComponent
```

As you can see here, there is no logic at all inside the component itself.

We pass the condition as the first parameter of the onlyIf function, and when the condition is matched, the component is rendered.

The function used to validate the condition receives the props, state, and context of the component.

In this way, we avoid polluting our component with conditionals so that it is easier to understand and reason about.

#### jsx-control-statements

This follows the same philosophy as JSX, and it does not add any real functionality to the language; it is just syntactic sugar that gets compiled into JavaScript. This is a Babel Plugin.

Wwe have to install it:

```
npm install --save jsx-control-statements
```

Once it is installed, we have to add it to the list of our Babel plugins in our ```.babelrc``` file:

```
"plugins": ["jsx-control-statements"]
```

From now on, we can use the syntax provided by the plugin and Babel will transpile it together with the common JSX syntax.

A conditional statement written using the plugin looks like the following snippet:

```jsx
<If condition={this.canShowSecretData}> 
  <SecretData /> 
</If>
```

This gets transpiled into a ternary expression as follows:

```jsx
{canShowSecretData ? <SecretData /> : null}
```

The If component is great, but if, for some reason, you have nested conditions in your render method, it can easily become messy and hard to follow. This is where the Choose component comes in handy:

```jsx
<Choose> 
  <When condition={...}> 
    <span>if</span> 
  </When> 
  <When condition={...}> 
    <span>else if</span> 
  </When> 
  <Otherwise> 
    <span>else</span> 
  </Otherwise> 
</Choose>
```

Please note that the preceding code gets transpiled into multiple ternaries.

Finally, there is a component (always remember that we are not talking about real components but just syntactic sugar) to manage the loops that is also very convenient:

```jsx
<ul> 
  <For each="user" of={this.props.users}> 
    <li>{user.name}</li> 
  </For> 
</ul>
```

The preceding code gets transpiled into a map function – no magic there.

If you are used to using linters, you might wonder why the linter is not complaining about that code. The user variable does not exist before the transpilation, nor is it wrapped in a function. To avoid those linting errors, there is another plugin to install: ```eslint-plugin-jsx-control-statements```.

## Object Destructuring

Destructuring is often the case that you have to access plenty of properties from your state or props in your component. Rather than assigning them to a variable one by one like the following example:

```jsx
const student = {
  ID: '21',
  name: 'Ana',
};

const id = student.ID;
const name = student.name;

console.log(id);
console.log(name);
```

You can use destructuring assignment in JavaScript:

```jsx
const student = {
  ID: '21',
  name: 'Ana',
};

const {ID, name} = student;
```

You can list the properties you want inside the curly brackets and even rename them using aliases like this:

```jsx
const student = {
  ID: '21',
  name: 'Ana',
};

const {name:n} = student;
console.log(n);
```

That’s especially beneficial for functional stateless components because they always receive the props object in their function signature. Often you will not use the props but its content, so you can destructure the content already in the function signature.

```jsx
// no destructuring
function Greeting(props) {
  return <h1>{props.greeting}</h1>;
}

// destructuring
function Greeting({ greeting }) {
  return <h1>{greeting}</h1>;
}
```

Another great feature is the **rest destructuring**. It is often used for splitting out a part of an object, but keeping the remaining properties in another object.

```jsx
// rest destructuring
const { users, ...rest } = this.state;
```

### React State

 useState hook will takes an initial state as an argument and the function will return an array with two values. The first value represents the current state; the second value is a function to update this state.

```jsx
const App = () => {
  const stories = [ ... ];

  // less readable version without array destructuring
  const searchTermState = React.useState('');
  const searchTerm = searchTermState[0];
  const setSearchTerm = searchTermState[1];

  ...
};
```

Consider using JavaScript array destructuring in React’s useState hook:

```jsx
const App = () => {
  const stories = [ ... ];


  const [searchTerm, setSearchTerm] = React.useState('');

  ...
};
```

## Spread Operator

Spread Operator literally spreads the contents of an array into its elements which makes operations like concatenation etc. easier. You can clone arrays using this operator.

Let’s say we want to concatenate two arrays, we can do this in different ways:

```jsx
// using concat function
a = [1,2,3];
b = [4,5,6];
c = a.concat(b);
console.log("c: " + c);
```

```jsx
// using Spread operator
a = [1,2,3];
b = [4,5,6];
c = [...a, ...b]; //spread operator
console.log("c: " + c);
```

You can easily add some elements in the middle of the two arrays: ```[...a, 'something', ...b];```

In React, you can combine two objects using Spread Operator and add extra properties to that object too, like this:

```jsx
const person = { name: "Jhon"};
const student = { ID: "21", GPA: "3.0"};

const new_object = { ...person, ...student, semester: '3'};
console.log(new_object); //output: { name: 'Jhon', ID: '21', GPA: '3.0', semester: '3' }
```

### Spreading properties on DOM elements

We usually spread the properties to the elements to avoid writing every single one manually, which is shown as follows:

```jsx
<Component {...props} />
```

This works very well and it gets transpiled into the following code by Babel:

```jsx
_jsx(Component, props)
```

However, when we spread properties into a DOM element, we run the risk of adding unknown HTML attributes, which is bad practice.

The problem is not related only to the spread operator; passing non-standard properties one by one leads to the same issues and warnings. Since the spread operator hides the single properties we are spreading, it is even harder to figure out what we are passing to the element.

To see the warning in the console, a basic operation we can do is render the following component:

```jsx
const Spread = () => <div foo="bar" />
```

The message we get looks like the following because the foo property is not valid for a div element:

```jsx
Unknown prop `foo` on <div> tag. Remove this prop from the element
```

In this case, as we said, it is easy to figure out which attribute we are passing and remove it, but if we use the spread operator, as in the following example, we cannot control which properties are passed from the parent:

```jsx
const Spread = props => <div {...props} />;
```

If we use the component in the following way, there are no issues:

```jsx
<Spread className="foo" />
```

This, however, is not the case if we do something such as the following. React complains because we are applying a non-standard attribute to the DOM element:

```jsx
<Spread foo="bar" className="baz" />
```

One solution we can use to solve this problem is to create a property called ```domProps``` that we can spread safely to the component because we are explicitly saying that it contains valid DOM properties.

For example, we can change the Spread component in the following way:

```jsx
const Spread = props => <div {...props.domProps} />
```

We can then use it as follows:

```jsx
<Spread foo="bar" domProps={{ className: 'baz' }} />
```

With React, **it's always good practice to be explicit.**

## Arrow Functions in React

Arrow Functions are a more concise and readable way of writing a function in JavaScript. They are frequently used in React to make things more efficient and simpler.

```jsx
// JavaScript ES5 function
function getGreetingFunc() {
  return 'Welcome to JavaScript';
}

// JavaScript ES6 arrow function with body
const getGreetingArrow1 = () => {
  return 'Welcome to JavaScript';
}

// JavaScript ES6 arrow function without body and implicit return
const getGreetingArrow2 = () =>
  'Welcome to JavaScript';

console.log(getGreetingFunc());
console.log(getGreetingArrow1());
console.log(getGreetingArrow2());
```

Another example:

```jsx
// JavaScript ES5 function
const students = [
  { ID: 1, present: true},
  { ID: 2, present: true},
  { ID: 3, present: false}, 
];

const presentStudents = students.filter(function(student){return student.present;});
console.log(presentStudents);
```

```jsx
// JavaScript ES6 arrow function
const students = [
  { ID: 1, present: true},
  { ID: 2, present: true},
  { ID: 3, present: false}, 
];

const presentStudents = students.filter(student => student.present);
console.log(presentStudents);
```

## Higher-Order Functions in React

Higher-order functions allows extracting functions. In this way, you make function testable in isolation from the React component. 


For example, if we have to rendered list of users can be filtered based on the value of an input field:

```jsx
import React from 'react';

export default class App extends React.Component {

  constructor(props){
    super(props);  
    
    this.state = {
    query: '',
    };
    
    this.onChange=this.onChange.bind(this);
  }
  
  onChange(event) {
    this.setState({ query: event.target.value });
  }
  
  render() {
  const users = [
      { name: 'Elsa' },
      { name: 'Juan' },
    
    ];
    return (
      <div>
        <ul>
          { users
            .filter(user => this.state.query === user.name)
            .map(myuser => <li>{myuser.name}</li>)
          }
        </ul>
        <input
          type="text"
          onChange={this.onChange}
        />
      </div>
    );
  }
}
```

The doFilter() function can be exported from the file and tested in isolation as pure (higher-order) function.

```jsx
import React from 'react';

const doFilter = query => user =>
   query === user.name;

export default class App extends React.Component {

  constructor(props){
    super(props);  
    
    this.state = {
    query: '',
    };
    
    this.onChange=this.onChange.bind(this);
  }
  
  onChange(event) {
    this.setState({ query: event.target.value });
  }
  
  render() {
  const users = [
      { name: 'Elsa' },
      { name: 'Juan' },
    
    ];
    return (
      <div>
        <ul>
          { users
            .filter(doFilter(this.state.query))
            .map(myuser => <li>{myuser.name}</li>)
          }
        </ul>
        <input
          type="text"
          onChange={this.onChange}
        />
      </div>
    );
  }
}
```

## Map, Reduce & Filter in React

A very common operation in UI development is to display lists of items. When it comes to showing lists, using JavaScript as a template language is a very good idea.

If we write a function that returns an array inside our JSX template, each element of the array gets compiled into an element.

We can use any JavaScript expressions inside curly braces, and the most common way to generate an array of elements, given an array of objects, is to use map.

For example:

```jsx
import React from 'react';

export default class App extends React.Component {
  render() {
    var users = [
      { name: 'Víctor' },
      { name: 'Milagros' },
    ];

    return (
      <ul>
           {users.map(user => <li>{user.name}</li>)}
      </ul>
    );
  }
}
```

The same can be applied for custom-tailored cases where filter() or reduce() make more sense rather than rendering an output for each mapped item.

```jsx
import React from 'react';

export default class App extends React.Component {
  render() {
    var users = [
      { name: 'Víctor', isDeveloper: true },
      { name: 'Milagros', isDeveloper: false },
      { name: 'Julieta', isDeveloper: true },
    ];

    return (
      <ul>
        {users
          .filter(user => user.isDeveloper)
          .map(user => <li>{user.name}</li>)
        }
      </ul>
    );
  }
}
```

## Sub-rendering

It is worth stressing that we always want to keep our components very small and our render methods very clean and simple.

However, that is not an easy goal, especially when you are creating an application iteratively, and in the first iteration, you are not sure exactly how to split the components into smaller ones. So, what should we be doing when the render method becomes too big to maintain? One solution is to split it into smaller functions in a way that lets us keep all the logic in the same component.

Let's look at an example:

```jsx
const renderUserMenu = () => { 
  // JSX for user menu 
} 
   
const renderAdminMenu = () => { 
  // JSX for admin menu 
} 
   
return ( 
  <div> 
    <h1>Welcome back!</h1> 
    {userExists && renderUserMenu()} 
    {userIsAdmin && renderAdminMenu()} 
  </div> 
)
```

This is not always considered best practice because it seems more obvious to split the component into smaller ones. However, sometimes it helps to keep the render method cleaner.

## Using indexes as a key

The key property uniquely identifies an element in the DOM, and React uses it to check whether the element is new or whether it has to be updated when the component properties or state change.

Using keys is always a good idea and if you don't do it, React gives a warning in the console (in development mode). However, it is not simply a matter of using a key; sometimes, the value that we decide to use as a key can make a difference. In fact, using the wrong key can give us unexpected behaviors in some instances. In this section, we will see one of those instances.

Let's create a List component, as shown here:

```jsx
import { FC, useState } from 'react'

const List: FC = () => {

}

export default List
```

Then we define our state:

```jsx
const [items, setItems] = useState(['foo', 'bar'])
```

The implementation of the click handler insert a new item at the top of the list:

```jsx
const handleClick = () => { 
  const newItems = items.slice()
  newItems.unshift('baz')

  setItems(newItems)
}
```

Finally, in render, we show the list and the + button to add the ```baz``` item at the top of the list:

```jsx
return ( 
  <div> 
    <ul> 
      {items.map((item, index) => ( 
        <li key={index}>{item}</li> 
      ))} 
    </ul> 

    <button onClick={handleClick}>+</button> 
  </div> 
) 
```

If you run the component inside the browser, you will not see any problems; clicking the + button inserts a new item at the top of the list.

Let's change render in the following way, adding an input field near each item. We then use an input field because we can edit its content, making it easier to figure out the problem:

```jsx
return ( 
  <div> 
    <ul> 
      {items.map((item, index) => ( 
        <li key={index}> 
          {item} 
          <input type="text" /> 
        </li> 
      ))} 
    </ul> 
    <button onClick={handleClick}>+</button> 
  </div> 
)
```

If we run this component again in the browser, copy the values of the items in the input fields, and then click +, we will get unexpected behavior. The items shift down while the input elements remain in the same position, in such a way that their value does not match the value of the items anymore.

What we can see is that React, instead of inserting the new element on top, swaps the text of the two existing elements, and inserts the last item at the bottom as if it was new. The reason it does that is that we are using the index of the map function as the key.

In fact, the index always starts from 0, even if we push a new item to the top of the list, so React thinks that we changed the values of the existing two, and added a new element at index 2. The behavior is the same as it would have been without using the key property at all.

This is a very common pattern because we may think that providing any key is always the best solution, but it is not like that at all. **The key has to be unique and stable, identifying one, and only one, item.**

To solve this problem, we can, for example, use the value of the item if we expect it not to be repeated within the list, or create a unique identifier.


## Handler Function in JSX

The **synthetic event** is a wrapper around the browser’s native event, with more functions that are useful to prevent native browser behavior (e.g. refreshing a page after the user clicks a form’s submit button). Always pass functions to these handlers, not the return value of the function, except when the return value is a function:

```jsx
// don't do this
<input
  id="search"
  type="text"
  onChange={handleChange()}
/>

// do this instead
<input
  id="search"
  type="text"
  onChange={handleChange}
/>
```

## Callback Handlers in JSX

There is no way to pass information as JavaScript data types up the component tree, since props are naturally only passed downwards. However, we can introduce a callback handler as a function.

```jsx
const App = () => {
  const stories = [ ... ];

  // Callback function gets introduced
  const handleSearch = event => {
    // Callback function is used elsewhere 
    console.log(event.target.value);
  };


  return (
    <div>
      <h1>My Hacker Stories</h1>

      <Search onSearch={handleSearch} />
      <hr />

      <List list={stories} />
    </div>
  );
};

const Search = props => {

  const [searchTerm, setSearchTerm] = React.useState('');

  const handleChange = event => {
    setSearchTerm(event.target.value);

    //  It “calls back” to the place it was introduced
    props.onSearch(event);

  };

  return ( ... );
};
```

## Inline Handler in JSX


Inline handlers allow us to execute the function right in the JSX. There are two solutions using the incoming function in the Item component as an inline handler. First, using JavaScript’s bind method:

```jsx
const Item = ({ item, onRemoveItem }) => (
  <div>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
    <span>
      <button type="button" onClick={onRemoveItem.bind(null, item)}>
        Dismiss
      </button>
    </span>
  </div>
);
```

Using JavaScript’s bind method on a function allows us to bind arguments directly to that function that should be used when executing it. The bind method returns a new function with the bound argument attached.

The second and more popular solution is to use a wrapping arrow function, which allows us to sneak in arguments like item:

```jsx
const Item = ({ item, onRemoveItem }) => (
  <div>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
    <span>

      <button type="button" onClick={() => onRemoveItem(item)}>
        Dismiss
      </button>
    </span>
  </div>
);
```

This is a quick solution because sometimes we don’t want to refactor a function component’s concise function body back to a block body to define an appropriate handler between function signature and return statement. While this way is more concise than the others, it can also be more difficult to debug because JavaScript logic may be hidden in JSX. It becomes even more verbose if the wrapping arrow function encapsulates more than one line of implementation logic, by using a block body instead of a concise body. This should be avoided:

```jsx
const Item = ({ item, onRemoveItem }) => (
  <div>
    ...
    <span>
      <button
        type="button"
        onClick={() => {
          // do something else

          // note: avoid using complex logic in JSX

          onRemoveItem(item);
        }}
      >
        Dismiss
      </button>
    </span>
  </div>
);
```

All three handler versions, two of which are inline and the normal handler, are acceptable. The non-inlined handler moves the implementation details into the function component’s block body; the inline handler move the implementation details into the JSX.


## React Fragments

A fragment wraps other elements into a single top-level element without adding to the rendered output. If you prefer to omit the wrapping ```<div>``` or ```<span>``` elements, substitute them with an empty tag that is allowed in JSX, and doesn’t introduce intermediate elements in our rendered HTML.

```jsx
const Search = ({ search, onSearch }) => (
 <div>

   <label htmlFor="search">Search: </label>
   <input
     id="search"
     type="text"
     value={search}
     onChange={onSearch}
   />

 </div>

);
```

```jsx
const Search = ({ search, onSearch }) => (
 <>
   <label htmlFor="search">Search: </label>
   <input
     id="search"
     type="text"
     value={search}
     onChange={onSearch}
   />
 </>
);
```

## Creating enum Types  using Symbols

Enums allow you to define constants with semantic names and unique values. Given that the values of symbols are different, they make excellent values for enumerated types.

```jsx
const directions = {
    UP   : Symbol( 'UP' ),
    DOWN : Symbol( 'DOWN' ),
    LEFT : Symbol( 'LEFT' ),
    RIGHT: Symbol( 'RIGHT' )
};
console.log(directions);
```

## Use Typescript

TypeScript is a superset of JavaScript which means that any feature in JavaScript is available in TypeScript.

The most prominent feature unique to TypeScript—the one that gave TypeScript its name—is, as noted, strong typing: a TypeScript variable is associated with a type that tells the compiler what kind of data it can hold. In addition, TypeScript does support type inference, and includes a catch-all any type, which means that variables don’t have to have their types assigned explicitly by the programmer; more on that in a moment. 

### Strongly-typed Function Component Props

React function components are just functions that take in a props parameter and return JSX. We can make them strongly-type function parameters.

Let’s start by adding a type annotation for the props:

```jsx
const Hello = (props: { who: string }) => (
  <p>Hello, {props.who}</p>
);
```

In this way, we have just made Hello's props strongly-typed.

Now, we can shorten the Hello component slightly by destructuring the props parameter:

```jsx
const Hello = ({ who }: { who: string }) => (
  <p>Hello, {who}</p>
);
```

**Using interface or type alias for the props is arguably more readable when the component has more than a couple of props.** This is also better if the type is referenced elsewhere in the app:

```jsx
type Props = { who: string }
const Hello = ({ who }: Props) => <p>Hello, {who}</p>
```

Also, our Hello component is an arrow function.

If we have a prop that is optional:

```jsx
type Props = { 
  who: string; 
  message?: string 
};
const Hello = ({ who, message }: Props) => (
  <React.Fragment>
    <p>Hello, {who}</p>
    {message && <p>{message}</p>}
  </React.Fragment>
);
```

message is ```undefined``` if it hasn’t been passed.

The short circuit operator (&&) is used to render the message only if it has been passed into the component. This is a typical pattern when rendering elements driven by optional props.

We can specifying a default for a function component prop. The first way is to use a static property called ```defaultProps``` on the function component. This is set to an object literal that contains the values to set the props to:

```jsx
Component.defaultProps = {
  prop1: value1,
  prop2: value2,
  ...
}
```

Continuing with the previous example:

```jsx
type Props = { who: string; message?: string };
const Hello = ({ who, message }: Props) => (
  <React.Fragment>
    <p>Hello, {who}</p>
    {message && <p>{message}</p>}
  </React.Fragment>
);
Hello.defaultProps = {
  message: "How are you?"
};
```

There is another way to provide default prop values to a function component. This approach is to use the standard syntax for default function parameters:

```jsx
const Component = ({ 
  prop1 = value1,
  prop2 = value2,
  ...
}: Props) => { ... }
```

Refactoring Hello component:

```jsx
type Props = { who: string; message?: string };
const Hello = ({ who, message = "How are you?" }: Props) => (
  <React.Fragment>
    <p>Hello, {who}</p>
    {message && <p>{message}</p>}
  </React.Fragment>
);
```

The default function parameter approach is arguably more readable than the ```defaultProps``` approach for defining default props. This is because the default value is closer to where the prop is defined. So, our eyes don’t need to scan right to the bottom of the component and back up again to fully understand a component’s props. It is also worth noting that ```defaultProps``` may be deprecated in React function components in a future release.

If the who prop is now an object containing name and friend properties and we add a renderMessage prop that is a function, so we can make the definition of the Props as the next:

```jsx
type Who = {
  name: string;
  friend: boolean;
}

type Props = {
  who: Who;
  message?: string;
  renderMessage?: (message: string) => React.ReactNode;
}

const Hello = ({
  who,
  renderMessage,
  message = "How are you?"
}: Props) => (
  <React.Fragment>
    <p>
      Hello, {who.name}
      {who.friend && " my friend"}
    </p>
    {message && (renderMessage ? renderMessage(message) : <p>{message}</p>)}
  </React.Fragment>
);
```

An example consumption of Hello is as follows:

```jsx
<Hello
  who={{ name: "Bob", friend: true }}
  message="Hey, how are you?"
  renderMessage={m => <i>{m}</i>}
/>
```

**Summarizing, create strongly-typed function props using the following syntax:**

```jsx
(param1: Type1, param2: Type2, ...) => ReturnType;
```

### Strongly-typed Function Component State

#### useState

In some cases, TypeScript can infer the type of the state returned from a ```useState``` hook from the initial value. So, state with the ```useState``` hook is automatically strongly-typed.

For the instances when TypeScriptss inference isn’t required, we can explicitly pass the state type into the ```useState``` generic parameter:

```jsx
const [...] = React.useState<StateType>(...);
```

For example:

```jsx
const [count, setCount] = React.useState<number | null>(null);
```

#### useReducer

The ```useReducer``` hook is excellent for implementing complex state scenarios. It uses a Redux like pattern for state management where object literals called actions are passed into a function called a reducer to change state.

Let’s create these below the State type:

```jsx
type Increment = {
  readonly type: 'increment';
  readonly incrementStep: number;
};
type Decrement = {
  readonly type: 'decrement';
  readonly decrementStep: number;
};
```

So, the actions will have a type property, which uniquely defines the type of action that needs to be performed along with the amount that the counter will need to be incremented or decremented. We have used the ```readonly``` keyword on the action properties so that they are immutable.

Next, we are going to create a union type called Actions from the Increment and Decrement types. This union type will eventually be used as the type for a parameter for the action in the reducer. Let’s add this under the Decrement type:

```jsx
type Actions = Increment | Decrement;
```

Moving on to the reducer function. Let’s create the function’s signature:

```jsx
const reducer = (state: State, action: Actions): State => {
  
};
```

Next, we’ll use a switch statement to handle each type of action, returning the new state in each branch:

```jsx
const reducer = (state: State, action: Actions): State => {
  switch (action.type) {
    case 'increment':
      return { count: state.count + action.incrementStep };
    case 'decrement':
      return { count: state.count - action.decrementStep };
  }
  return state;
};
```

We are now going to make the reducer super type-safe by leveraging the never type.

```jsx
const reducer = (state: State, action: Actions): State => {
  switch (action.type) {
    ...
    default:
      neverReached(action);
  }
  return state;
};

const neverReached = (never: never) => {};
```

This allows to the editor reminded us to implement the additional switch branch in the reducer if we don't implemented. TypeScript will know that there is an action type that we haven’t handled. The ```never``` type in the switch statement’s default branch to tell the TypeScript compiler that this should never be reached:

Now that we have fully implemented the reducer, we can use it within the Counter component using the ```useReducer``` hook.

```jsx
const Counter = ( ... ) => {
  const [state, dispatch] = React.useReducer(reducer, { count: initialCount });

  return ( ... );
};
```

The type of state is ```State``` and the type of the dispatch function is ```Dispatch<Actions>```. TypeScript has cleverly inferred these types. We can explicitly define the types by passing them into the generic parameters of ```useReducer```:

```jsx
const Counter = ( ... ) => {
  const [state, dispatch] = React.useReducer<React.Reducer<State, Actions>>(
    reducer, 
    { count: initialCount }
  );

  return ( ... );
};
```

We can complete our first implementation of the Counter component by rendering the current count and dispatching the actions when the buttons are clicked:

```jsx
const Counter = ( ... ) => {
  ...
  return (
    <div>
      <div>{state.count}</div>
      <button onClick={() => dispatch({ type: "increment", incrementStep })}>
        Add {incrementStep}
      </button>
      <button onClick={() => dispatch({ type: "decrement", decrementStep })}>
        Subtract {decrementStep}
      </button>
      <button onClick={() => dispatch({ type: "double" })}>
        Double
      </button>
    </div>
  );
};
```

### Strongly-typed Events

We have an input with an onChange event handler inline:

```jsx
<input
  type="text"
  value={criteria}
  onChange={e => setCriteria(e.currentTarget.value)}
/>
```

The parameter type for inline event handlers is always inferred correctly. ```e``` is of type ```React.ChangeEvent<HTMLInputElement>```.

Let’s refactor the implementation to use a named function for the event handler:

```jsx
function Searchbox() {
  const [criteria, setCriteria] = React.useState("");

  const handleChange = e => setCriteria(e.currentTarget.value);

  return (
    <input
      type="text"
      value={criteria}
      onChange={handleChange}
    />
  );
}
```

What is the type of ```e``` now? ```e``` is now of type ```any``` because TypeScript can’t infer this. So, our named function event handler is not strongly-typed. 

To make handleChange strongly-typed we need to add the parameter type:

```jsx
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) =>
  setCriteria(e.currentTarget.value);
```

So, the type for event props is:

```jsx
(param1: type1, param2: type2, ...) => void
```

Notice that the return type is void, which signifies nothing is returned from the function.

### Strongly-typed Context

React context allows several components in a tree to share some data. It’s more convenient than passing the data via props down the component tree. The context is provided at a point in the component tree, and then all the children of the provider can access the context if they wish.

Let’s start by creating our theme using Reacts createContext function:

```jsx
const defaultTheme = "white";
const useTheme = () => React.useContext(ThemeContext);
```

The return type of ```useTheme``` is string because this is the type of the context value. The type for the context is inferred correctly if a sensible default is provided when it is created. If the context is providing values that consumers don’t change, then this is fine. However, what if we want the user to change the theme? In this case, our context would need to provide a function for updating the theme, and this can’t really be provided as a default value.

So, enhancing context is going to be a little more complex:

```jsx
type ThemeContextType = {
  theme: string;
  setTheme: (value: string) => void;
};
```

React’s ```createContext``` function expects us to supply a parameter for initial context value. We can supply a default value for the theme property, but it doesn’t make sense to provide a default implementation for the ```setTheme``` method. So, a simple approach is to pass in ```undefined``` as the initial value:

```jsx
const ThemeContext = React.createContext(undefined);
```

The type of the context value is inferred to be ```undefined``` if in strict mode or any if not.

So, ```ThemeContext``` isn’t typed as we require at the moment. But ```createContext``` is a generic function. So, we can pass in the type for the Context value as a generic parameter:

```jsx
const context = React.createContext<ContextType>(...)
```

Therefore, we can type our context as follows:

```jsx
const ThemeContext = React.createContext<ThemeContextType | undefined>(
  undefined
);
```

### Strongly-typed refs in function components

The ```useRef``` hook can be used to access all the properties and methods of an element.

Below is an example of using the useRef hook:

```jsx
const Search = () => {
  const input = React.useRef(null);
  React.useEffect(() => {
    if (input.current) {
      input.current.focus();
    }
  }, []);
  return (
    <form>
      <input ref={input} type="search" />
    </form>
  );
};
```

The type of input.current is inferred to be ```null``` if strict mode is on, otherwise it is inferred to be ```any```.

We can explicitly define the type of the element returned from ```useRef``` by passing a generic type parameter:

```jsx
const element = React.useRef<ElementType>(null);
```
