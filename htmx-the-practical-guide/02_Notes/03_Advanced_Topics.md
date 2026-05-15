# HTMX The Practical Guide

## 03. Advanced Topics

### 03.19 Module Introduction

The module covers:

- **Attribute inheritance**
- Advanced **content selection** and **DOM manipulation**
- **Events** and **custom JavaScript**
  - HTMX generated events,
  - and non HTMX generated events
- **Configuration** and **extensions**

### 03.20 Startup Project

Reusing the last from Section 2.

### 03.21. Sending DELETE Requests

Implement a DELETE endpoint that returns nothing:

```js
app.delete('/goal/:id', (req, res) => {
  const { id } = req.params;
  const index = courseGoals.findIndex(goal => goal.id === parseInt(id));
  if (index !== -1) {
    courseGoals.splice(index, 1);
  }
  res.send();
});
```

The element to delete an item sets `outerHTML` to self

```html
            <li id="goal-${goal.id}">
              <span>${goal.id}: ${goal.goal}</span>
              <button
                hx-delete="/goal/${goal.id}"
                hx-target="#goal-${goal.id}"
                hx-swap="outerHTML">Remove</button>
            </li>
```

### 03.23. HTMX inheritance

We repeat the attribute `hx-swap="outerHTML"` to all the buttons both when we return the page initially and when we create new TODO item.

Most HTMX attributes can be set on the parent node and inherited down on the DOM.

So, we add it to the `ul`:

```html
<ul id="goals" hx-swap="outerHTML">
```

Also, we can use `delete` instead of `outerHTML`:

```html
<ul id="goals" hx-swap="delete">
```

See: <https://htmx.org/attributes/hx-swap/>
