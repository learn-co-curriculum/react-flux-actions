Actions and Action Creators
===========================

In this lesson, we will delve into the topic of Actions and Action Creators. By
the end of the lesson you will be able to:

1. Describe conventions for naming actions
2. Describe patterns for writing actions and action creators
3. Describe how information gets to the action creator

## Everything Begins With an Action

In Flux applications, as we know, there is a simple pathway that all processes
follow:

![Basic Flux Diagram](https://camo.githubusercontent.com/531c8327584413af6cdc8aa2dca87106d053345e/68747470733a2f2f657a6d696c6c65722e73332e616d617a6f6e6177732e636f6d2f7075626c69632f696d616765732f666c75782d73696d706c6573742e6a7067)

That pathway begins, as we can see above, with an **Action**. Before we jump into
the code patterns for generating actions, let's refresh our understanding of actions.
There are four main points to keep in mind:
* Actions represent a single event in the application.
* Actions can be triggered both by user action and changes on the server.
* After an action has moved through the system, the application will be in a new state.
* In Flux applications, **only one action can move through the system
at a time**.

## How do we represent Actions in Code?

At base, actions are just simple JavaScript objects consisting of a property
identifying the action _type_, along with any other data necessary to carry
out the action. That's is.

The following is a basic example of an action that you might see in a ToDo App:

```javascript
{
  actionType: 'ADD_TODO',
  payload: {
    text: 'Write a ToDo App using Flux'
  }
}
```

## Dispatching Actions

So now that we know that an action is just a JavaScript object, you may be wondering
how actions move through a Flux system. This is a very good question, and although
we will cover it again, we can illustrate the basic procedure here. It's quite simple!

The first step of course is to dispatch an action. Here we make use of a function
exposed on all Flux dispatchers: `dispatch`. The usage is simple. To dispatch an
action all we'd need to do is this:

```javascript
Dispatcher.dispatch({
  actionType: 'ADD_TODO',
  payload: { text: 'Write a ToDO App using Flux' }
});
```

At this point, the Dispatcher will have queued our action. The next step is for
it to be sent to the stores. Each Store in a Flux Application registers itself
with the Dispatcher, providing a callback. Within that callback, each store specifies
the _types_ of actions to which it repsonds. We won't go into this in detail now,
but the code is usually handled with a simple, efficient, switch statement.
Something like this:

```javascript
swtich (action.actionType) {
  case 'ADD_TODO':
    // Call appropriate Store method(s) to handle action.
}
```

## Patterns for Actions

Now that we know the rough outline for how actions move through a Flux application,
let's look at some of the different ways that people manage and create actions. There
are a few different patterns that have developed over time, and you will likely 
encounter a range of practices.

#### The Most Basic (Bad) Pattern

First, though, let's talk about a pattern that is generally **not** used. Probably, the
most basic way to deal with actions would be just to generate an action object
in-place, wherever you need to dispatch, i.e. precisely what we did in the simple
example above.

However, this is not a good practice. Who wants to repeatedly write out JavaScript
action objects, when we don't have to? We can definitely improve on this.

#### Creating Actions Via Helper Functions (aka Action Creators)

The most common practice by far is to build a series of helper functions that accept
a payload, and then generate and dispatch the action. These are alternately just called
"Action Creators" or just "Actions." They look like this:

```javascript
const addTodo = (text) => {
  Dispatcher.dispatch({
    actionType: 'ADD_TODO',
    payload: { 'text': text }
  });
};
```
So what do we have here? Well it's pretty simple the function in this case is
one we'd call when creating a ToDo. It takes a payload, in this case the text
of the new ToDo, and then it makes a call to the dispatcher, supplying the action
itself. The `actionType` is set appropriately, and the payload value is attached.

Now when we want to dispatch an add todo action, we just need to call it like
so:

```javascript
handleAddToDo() {
  addTodo('Write a ToDo App using Flux.');
}
```
What are the benefits of this approach? They are multiple. We avoid the need to
repeatededly write out an JavaScript action object each time, and because of that
we also make our code less buggy. We prevent that inherent bugginess that
can come from typos. Also, if we were to need to change the structure of our
action in the future, we'd only need to make that change in action creator.
This is smart programming!

But where do we put these action creators? This is a good question. The usual
practice is to create a folder `actions` to house our actions. To make things
even more stable, we can also include a `constants` folder to hold  action type
constants so that we don't run the risk of mistyping our action names throughout
the system. Here's how this would look:

#### `constants/ToDoConstants.js`

```javascript
const ToDoConstants = {
  ADD_TODO: 'add_todo',
};

module.exports = ToDoConstants;
```

#### `actions/ToDoActions.js`
```javascript
const ToDoActions = require('./ToDoActions');
const Dispatcher = require('../dispatcher/Dispatcher');

const addToDo = (text) => {
  Dispatcher.dispatch({
    actionType: ToDoActions.jsx
  })
}

module.exports = {
  'addToDo': addToDo
};
```


## Resources

- Facebook React Blog, [Flux Actions and the Dispatcher](https://facebook.github.io/react/blog/2014/07/30/flux-actions-and-the-dispatcher.html#actions-and-actioncreators)
- Facebook Flux Documentation, [Actions and Action Creators](https://facebook.github.io/flux/docs/actions-and-the-dispatcher.html#actions-and-action-creators)
