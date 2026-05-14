# HTMX The Practical Guide

## 02. HTMX Essentials

### Links

Link to different web sites:

- [htmx](https://htmx.org/)

### 02.07. Installing htmx

We can download or include from CDN.

We could also `npm install` but we will need to setup a build process to produce client side bundle.

We save a copy in *01_Code/02_Essentials/public/htmx.js*. Then we include with `<script src="/htmx.js" defer></script>`.

We use `defer` to make sure it loads only after the page has been rendered. Thereof if we are using htmx anywere, the script will be ready to handle this.

### 02.08. Using HTMX & sending GET request

We use `hx-get` which sends a GET request. We provide a path. It fires by default on click. By. default it outputs the response from server into the element that triggered the request.

```html
<button hx-get="/info" hx-target="#main-content">Learn More</button>
```

Then we provide a handler to deliver html fragment from the server:

```js
app.get('/info', (req, res) => {
  res.send(`
    <ul>
      ${HTMX_KNOWLEDGE.map(item => `<li>${item}</li>`).join('')}
    </ul>
  `);
});
```

### 02.09. Controlling response content with hx-swap

We use `hx-swap` attribute, so that instead of outputting the response inside the button, we replace the button. We specify what we want to swap. `innerHtml` is the default. Let's use `outerHtml`:

```js
<button hx-get="/info" hx-swap="outerHTML">Learn More</button>
```

### 02.11. Response target with hx-target

If we want to render elsewhere we use `hx-target`. It uses css selector to target.

We still can use `hx-swap` to define what part of the target we replace.

```html
<button hx-get="/info" hx-target="main" hx-swap="outerHTML">Learn More</button>
```

. or by default replacing the innerHTML:

```html
<main>
  <p>HTMX is a JavaScript library that you use without writing JavaScript code.</p>
  <button hx-get="/info" hx-target="main">Learn More</button>
</main>
```

, or we could append before the end of the target:

```html
<main>
  <p>HTMX is a JavaScript library that you use without writing JavaScript code.</p>
  <button hx-get="/info" hx-target="main" hx-swap="beforeend">Learn More</button>
</main>
```

If we target collection of elements, only the first will get targeted.

### 02.12. Changing the request trigger with hx-trigger

By default the event that triggers the request is the most obvious event for that element. For form is submit. For button, and most elements, it is on click.

We could do on mouse enter:

```html
<button hx-get="/info" hx-trigger="mouseenter" hx-target="main" hx-swap="beforeend">Learn More</button>
```

Or mouse over with control key pressed:

```html
<button hx-get="/info" hx-trigger="mouseenter[ctrlKey]" hx-target="main" hx-swap="beforeend">Learn More</button>
```

Or use combination of events separated with comma:

```html
<button hx-get="/info" hx-trigger="mouseenter[ctrlKey],click" hx-target="main" hx-swap="beforeend">Learn More</button>
```

We can say - only on first click:

```html
<button hx-get="/info" hx-trigger="mouseenter[ctrlKey],click once" hx-target="main" hx-swap="beforeend">Learn More</button>
```

Or you could change the element that triggers the request:

```html
<button>Load Data</button>
<div hx-get="/data" hx-trigger="click from:previous button">
    Nothing there :(
</div>
```

Or we can also trigger the request once the element is scrolled into the viewport:

```html
<div hx-get="/data" hx-trigger="revealed">
    Nothing there :(
</div>
```

See: [triggers documentation](https://htmx.org/docs/#triggers)

#### 02.14. POST request

Create a form with `hx-post` attribute. Note: does not need to be a form, any element can trigger a post.

On a form, it automatically gets triggered on form submit. Obviously we could change the event with `hx-trigger`:

```html
<form hx-post="/note" hx-target="ul" hx-swap="outerHTML">
  <p>
    <label for="note">Your note</label>
    <input type="text" id="note" name="note" />
  </p>
  <ul>...</ul>
```

We also set attribute `hx-target="ul"` so we target the first `ul` element inside the form. And we also set attribute `hx-swap="outerHTML"`, so that we completely replace the `ul` node instead of just it's inner content.

The post will submit the note by `name="note`" attribute, or whatever the value of the `name` attribute is.

On the server we configure middleware, so that node scans all the request form data and adds to the request object as `req.body`:

```js
app.use(express.urlencoded({ extended: false }));
```

Now we create method to handle the post. By default, the returned html will replace the innerHTML of the form:

```js
app.post('/note', (req, res) => {
  const { note } = req.body;
  HTMX_KNOWLEDGE.unshift(note);
  // usually we would do `response.redirect()`, so that the user can see the new note in the list.
  res.send(`
    <ul>
      ${HTMX_KNOWLEDGE.map(item => `<li>${item}</li>`).join('')}
    </ul>
  `);
});
```

#### 02.17. Picking parts of response with hx-select

We could use `hx-select` to tell HTMX which part from the response to insert into he target element. We specify via CSS selector what part of the HTML response we want to select.

```html
  <form
    hx-post="/note"
    hx-target="ul"
    hx-swap="innerHTML"
    hx-select="ul li:first-child"
  >
```
