---
title: Server-side rendering vs Client-side rendering
layout: post
subtitle:  Which method is the better option? As with most things in development, it really depends on what you’re planning on doing with your website. You need to understand the pros and cons, then decide for yourself which route is best for you.
categories: web
tags: [javascript, frontend]
---


## server-side rendering

Server-side rendering is the most common method for displaying information onto the screen. It works by converting HTML files in the server into usable information for the browser.

Whenever you visit a website, your browser makes a request to the server that contains the contents of the website. The request usually only takes a few milliseconds. Once the request is done processing, your browser gets back the fully rendered HTML and displays it on the screen. If you then decide to visit a different page on the website, your browser will once again make another request for the new information. This will occur each and every time you visit a page that your browser does not have a cached version of.

It doesn’t matter if the new page only has a few items that are different than the current page, the browser will ask for the entire new page and will re-render everything from the ground up.

Take for example this HTML document that has been placed in an imaginary server with an HTTP address of example.testsite.com.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Example Website</title>
  </head>
  <body>
    <h1>My Website</h1>
    <p>This is an example of my new website</p>
    <a href="http://example.testsite.com/other.html.">Link</a>
  </body>
</html>
```

If you were to type the address of the example website into the URL of your imaginary browser, your imaginary browser would make a request to the server being used by that URL and expect a response of some text to render onto the browser. In this case, what you would visually see would be the title, the paragraph content and the link.

Now, assume that you wanted to click on the link from the rendered page which contains the following code.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Example Website</title>
  </head>
  <body>
    <h1>My Website</h1>
    <p>This is an example of my new website</p>
    <p>This is some more content from the other.html</p>
  </body>
</html>
```

The only difference between the previous page and this one is that this page does not have a link and instead has another paragraph. Logic would dictate that only the new content should be rendered and the rest should be left alone. What would actually happen in server-side rendering would be that the entire new page would be rendered, and not just the new content.

While it might not seem like a big deal for these two examples, most websites are not this simple. Modern websites have hundreds of lines of code and are much more complex. Now imagine browsing a webpage and having to wait for each and every page to render when navigating the site. If you have ever visited a WordPress site, you have seen how slow they can be. This is one of the reasons why.

On the bright side, server-side rendering is great for SEO. Your content is present before you get it, so search engines are able to index it and crawl it just fine. Something that is not so with client-side rendering. At least not simply.

### Pros
- Search engines can crawl the site for better SEO.
- The initial page load is faster.
- Great for static sites.

### Cons
- Frequent server requests.
- An overall slow page rendering.
- Full page reloads.
- Non-rich site interactions.

### When to use

- An application has a very simple UI with fewer pages/features
- An application has less dynamic data
- Read preference of the site is more than write
- The focus is not on rich sites and has few users

## client-side rendering

When developers talk about client-side rendering, they’re talking about rendering content in the browser using JavaScript. So instead of getting all of the content from the HTML document itself, you are getting a bare-bones HTML document with a JavaScript file that will render the rest of the site using the browser.

It become popular when JavaScript libraries started incorporating it into their style of development. Some notable examples are Vue.js and React.js.

Going back to the previous website, example.testsite.com, assume that you now have an index.html file with the following lines of code.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Example Website</title>
</head>
<body>
  <div id="root">
    <app></app>
  </div>
  <script src="https://vuejs.org"type="text/javascript"></script>
  <script src="location/of/app.js"type="text/javascript"></script>
</body>
</html>
```

For starters, instead of having the content inside the HTML file, you have a container div with an id of root. You also have two script elements right above the closing body tag. One that will load the Vue.js JavaScript library and one that will load a file called app.js.

This is radically different than using server-side rendering because the server is now only responsible for loading the bare minus of the website. The main boilerplate. Everything else is handled by a client-side JavaScript library.

If you were to make a request to the URL with only the code above, you would get a blank screen. There is nothing to load since the actual content needs to be rendered using JavaScript.

To fix that, you would place the following lines of code into the app.js file.

```javascript
var data = {
        title:"My Website",
        message:"This is an example of my new website"
      }
  Vue.component('app', {
    template:
    `
    <div>
    <h1>{{title}}</h1>
    <p id="moreContent">{{message}}</p>
    <a v-on:click='newContent'>Link</a>
    </div>
    `,
    data: function() {
      return data;
    },
    methods:{
      newContent: function(){
        var node = document.createElement('p');
        var textNode = document.createTextNode('This is some more content from the other.html');
        node.appendChild(textNode);
        document.getElementById('moreContent').appendChild(node);
      }
    }
  })
  new Vue({
    el: '#root',
  });
```

Now if you visit the URL, you would see the same content as you did the server-side example. The key difference is that if you were to click on the link the page to load more content, the browser will not make another request to the server. You are rendering items with the browser, so it will instead use JavaScript to load the new content and Vue.js will make sure that only the new content is rendered. Everything else will be left alone.

The benefit of client-side rendering is that it resolves the main pain point of server-side rendering; getting more content and/or changing pages doesn’t mean the browser needs to make another request to the server, rather the browser is responsible for rendering content by using JavaScript to load the new content. This allows for the page to load faster since you only need to load the portion of the page that needs the new content, thus avoiding an entire page loading for a small portion. However, since content isn’t rendered until the page loads on the browser, SEO is dramatically affected and your website won’t be able to load until all of the JavaScript is downloaded to the browser, making initial loading times long depending on the internet connection. Therefore, client-side rendering is better for web applications where content is constantly changing, and fast response/rendering is important and based on lots of user interactions.

Another thing to keep in mind is that your website/application won’t be able to load until all of the JavaScript is downloaded to the browser. Which makes sense, since it contains all the content that will be needed. If your users are using slow internet connection, it could make their initial loading time a bit long. This could be solved with lazy loading mechanism.

### Pros
- Rich site interactions
- Fast website rendering after the initial load.
- Great for web applications.
- Robust selection of JavaScript libraries.

### Cons
- Low SEO if not implemented correctly.
- Initial load might require more time.
- In most cases, requires an external library.

### When to use

- An application has a very complex UI with many pages/features
- An application has large and dynamic data
- Write preference of the site is more than reading
- The focus is on rich sites and a huge number of users

## Sources

- [Client-side vs. server-side rendering: why it’s not all black and white](https://www.freecodecamp.org/news/what-exactly-is-client-side-rendering-and-hows-it-different-from-server-side-rendering-bd5c786b340d/)
- [Server-Side Rendering (SSR) Vs Client-Side Rendering (CSR)](https://dev.to/codewithtee/server-side-rendering-ssr-vs-client-side-rendering-csr-3m24)
- [All About Rendering: Client and Server](https://javascript.plainenglish.io/all-about-rendering-client-and-server-efc94b117460)