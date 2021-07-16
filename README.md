# ![NANNY STATE](https://user-images.githubusercontent.com/16646/125978720-deaca09e-5361-4a51-918f-bbc4a3a7b841.png)

Simple state management using just JavaScript

Nanny State gives you simple state management and fast rendering with a tiny amount of code.
It was inspired by [Redux](https://redux.js.org) and [Hyperapp](https://hyperapp.dev) and uses the [lit-html](https://lit-html.polymer-project.org) library for rendering.

# What Is Nanny State?

An app built with Nanny State is made up of the following:

* State - usually an object that contains all the data about the app
* View -  a function that returns a string of HTML based on the current state
* Transformer functions - functions that transform the value of the state

The state is the single source of truth in the application and can only be updated using the update function provided by Nanny State.

## Data Flow

Nanny state uses a one-way data flow model:

![Nanny State](https://user-images.githubusercontent.com/16646/125978059-95ed42bb-5676-484a-8391-fa73d20280a0.png)

When a user interacts with the page, it triggers an event that is handled by an event handler.

A tranformer function is used to update the state. Whennever the state changes, the page is re-rendered to reflect these changes.

# Examples

The easiest way to learn how Nanny State works is to try coding some examples. All the examples below can be coded on [CodePen](https://codepen.io) by simply entering the code in the 'JS' section. Alternatively you could set up a basic HTML file with a linked JS file that contains all the Nanny State code.

## Hello World Example

![Screenshot Hello World](https://user-images.githubusercontent.com/16646/125823073-d88989b7-f807-4213-a871-f5f41e198f23.png)

This is a simple example to show how Nanny State renders the view based on the state.

You can see finished app and code [on CodePen](https://codepen.io/daz4126/pen/zYwZjWw).

Start by importing the Nanny State functions:

```javascript
import {Nanny,html,render} from 'https://daz4126.github.io/Nanny-State/main.js'
```

Now create an object to represent the initial state (the state is usally an object, but can be any data-type):

```javascript
const state = { name: ‘World’ }
```

Now create the view - this is a function that accepts the state as a parameter and returns a string of HTML that depends on the value of the state's properties. All Nanny State view code needs to use the `html` template function provided by lit-html. This uses tagged template literals with `${expression}` placeholders to insert values from the state into the HTML. In this example we are inserting the value of the state object's 'name' property into the `<h1>` element.:

```javascript
const view = state => html`<h1>Hello ${state.name}</h1>`
```

Now call the `Nanny` function, providing the `state` and `view` variables as arguments:

```javascript
Nanny(state,view)
```

This will render the view using the initial state.

## Hello Batman Example
![Screenshot Hello Batman](https://user-images.githubusercontent.com/16646/125826661-0b799f2d-613d-45b8-9bef-5c0d214fe669.png)

This example shows how the state object can be updated using the Nanny State update function.

You can see the finished app and code [on CodePen](https://codepen.io/daz4126/pen/oNWZdyd).

It starts in the same way as the last example, by importing the Nanny State functions and initializing the state:

```javascript
import {Nanny,html,render} from 'https://daz4126.github.io/Nanny-State/main.js'
const state = { name: 'Bruce Wayne' }
```

Next we'll add the view template:

```javascript
const view = state => html`<h1>Hello ${state.name}</h1>
                           <button @click=${beBatman}>I'm Batman</button>`
```

This view contains a button that has an inline event listener attached to it using the `@event${handler}` notation [used by lit-html](https://lit-html.polymer-project.org/guide/writing-templates#add-event-listeners)). When the button is clicked the event handler 'beBatman' will be called. We want this function to update the state object so the 'name' property changes to 'Batman'.

The only way to update the state when using Nanny State is to use the update function that is returned by the `Nanny` function.

Calling the `Nanny` function does 2 things:
1) It renders the initial view based on the intial state provided as an argument (as we saw in the Hello World example).
2) It also returns an update function that is the only way to update the state.

To be able to use the update function, we need to assign it to a variable when we call the `Nanny` function. We usually call it `update` but it can be called anything you like:

```
const update = Nanny(state,view)
```

The `update` function can now be used to update the state. Nanny State will then re-render the view using lit-html, which only updates the parts of the view that have actually changed. This makes re-rendering after a state update blazingly fast.

To see this in action, let's write the 'beBatman' event handler function to update the state and change the state object's 'name' property to 'Batman' when the button is clicked (note that this function needs to go *before* the `view` function in your code):

```javascript
const beBatman = event => update(state => ({ name: 'Batman'}))
```

Because this is an event handler, the only parameter is the event object (although it isn't actually needed in this example). The purpose of this event handler is to call the `update` function. This accepts an anonymous function as an argument that tells Nanny State how to update the state. This anonymous function is a **transformer function** that tells Nanny State how to update the value of the state. 

A transformer function accepts the current state as an argument and returns a new representation of the state:
![transformer](https://user-images.githubusercontent.com/16646/125978502-29d3f173-626a-48b1-8214-5368f1fe7824.png)

In this example the transformer function returns a new object with the 'name' property of 'Batman'.
**Note that when arrow functions return an object, the object needs wrapping in parentheses**

Everything is now in place and wired up. Try clicking the button and you'll see the view change based on user input!

## Counter Example

The next example will be a simple counter app that lets us increase or decrease the count by pressing buttons. The state will change with every click of a button, so this example will show how easy Nanny State makes dynamic updates.

![Screenshot Counter Example](https://user-images.githubusercontent.com/16646/125827676-f8510690-5b2e-4e98-b8b2-d00b8f530061.png)

You can see this example [on CodePen](https://codepen.io/daz4126/pen/vYgdLdX!)


The value of the count will be stored in the state as a number (The state is usually an object, but it doesn't have to be).
Let's initialize it with a value of 10:

```javascript
import {Nanny,html,render} from 'https://daz4126.github.io/Nanny-State/main.js'
const state = 10;
```

Now let's create the view that will return the HTML we want to display:

```javascript
const view = number => html`<h1>Nanny State</h1>
                            <h2>Counter Example</h2>
                            ${Counter(number)}`
```

This view contains a **component** called `Counter`. A component is a function that returns some view code that can be reused throughout the app. They are easy to insert into the main view using the `${ComponentName}` placeholder notation inside the template literal.

Let's write the code for the `Counter` component (note it is convention to use PascalCase when naming components):

```javascript
const Counter = number => html`<div id='counter'>${number}</div>
                               <button @click=${down}>-</button>
                               <button @click=${up}>+</button>`
```

The two buttons call the two event handlers, `down` and `up` that deal with changing the value of the count when they are pressed. We're going to need to transformer function to deal with incrementing this value, so let's write it:

```javascript
const increment = (number,i=1) => number + i
```

This function accepts two parameters: the number to be incremented and the amount it is to be incremented by, which has a default value of `1`. We could make this value negative to make the count go down. Now we can write the event handlers that use this transformer function to update the state:

```javascript
const up = event => update(increment)
const down = event => update(increment,-1)
```

Both these event handlers pass the `increment` transformer function to Nanny State's update function. The `up` handler uses the default parameter of `1`, so no argumennts need providing. The `down` handler provides an argument of `-1` so that the value of the state will decrease by 1.
**The first parameter of every transformer functions is always the state. This is always implicityly provided as an argument by the update function, so does not need to be included when calling `update`. Any additional arguments are added after the name of the function.**

Last of all, we just need to call the `Nanny` function and assign its return value to the variable `update`:

```
const update = Nanny(state,view)
```

This should render the initial view with the count set to 10 and allow you to increase or decrease the count by clicking on the buttons.

## More Examples

You can see more examples of how Nanny State can be used [on CodePen](https://codepen.io/collection/RzbNmw)

# TLDR

In summary, all you need to do to create a Nanny State app is the following:

1) import the Nanny State functions using `import {Nanny,html,render} from 'https://daz4126.github.io/Nanny-State/main.js'`
2) Create the initial state
3) Create the view template
4) Write event handlers to deal with user input
5) Write transformer functions that update the state
6) Call the `Nanny(state,view)` function

# Extra Info

## Before & After Functions

Before and after functions can be used to run any functions before or after a state update. These are passed to the `Nanny` function as part of the `options` object.

For example, try updating the last line of the 'Hello Batman' example to the following code instead: 

```
const before = state => console.log('Before:',state)
const after = state => console.log('After:',state)

const update = Nanny(state,view,{ before, after })
```

Now, when you press the "I'm Batman" button, the following is logged to the console, showing how the state has changed:

```bash
"Before:"
{
  "name": "Bruce Wayne"
}
"After:"
{
  "name": "Batman"
}
```

The after function is useful if you want to use the `localStorage` API to save the state between sessions. The following `after` function will do this:

```javascript
const after = state => localStorage.setItem('NannyState',JSON.stringify(state)) }
```

You will also have to set `state` to be the value stored in `localStorage` or the initial state if there is not value in local storage:

```javascript
const initialState = { name: 'World }
const state = localStorage.getItem('NannyState') ? JSON.parse(localStorage.getItem('NannyState')) : initialState
```

## Default Element

By Default the view will be rendered inside the `body` element of the page. This can be changed using the `element` property in the `options` object of the `Nanny` function. For example, if you wanted the view to be rendered inside an element with the id of 'root', you could use the following code:

```javascript
const update = Nanny(state,view,{element: document.getElementById('root')}
```

## Transformer Function & Fragments of State

Transformer functions don't need to return an object that represents the full state. You only need to return an object that contains the properties that have changed. For example, if the initial state is represented by the following object:

```javascript
const state = {
  name: World,
  count: 10
}
```

If we write a transformer function that doubles the count, then we only need to return an object that shows the new value of the 'count' property, like so:

```javascript
const double = state => ({ count: state.count * 2})
```

We can also use destructuring to only use the parts of the state we require in the parameter as well:

```javascript
const double = { count } = ({ count: count * 2})
```


## Coming Soon
A more in-depth tutorial to build a [Caesar Cipher tutorial using Nanny State](https://codepen.io/daz4126/pen/OJprPrL).



