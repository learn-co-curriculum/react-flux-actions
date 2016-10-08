Actions and Action Creators
===========================

In this lesson, we will delve into the topic of Actions and Action Creators. By
the end of the lesson you will be able to:

1. Describe conventions for naming actions
2. Describe patterns for writing actions and action creators
3. Describe how information gets to the action creator

## Everything Begins With an Action

In Flux applications, as we know, there is a simple pathway that actions **always**
follow as they move through the system. Here's that pathway again:

![Basic Flux Diagram](https://camo.githubusercontent.com/531c8327584413af6cdc8aa2dca87106d053345e/68747470733a2f2f657a6d696c6c65722e73332e616d617a6f6e6177732e636f6d2f7075626c69632f696d616765732f666c75782d73696d706c6573742e6a7067)

As we can see above, this pathway begins when an  **Action** is dispatched to
the dispatcher. We've learned about actions already, but let's review what they
are quickly before we examine the code for generating and dispatching
them.

There are four main points to keep in mind:
* Actions represent a single event in the application.
* Actions are usually triggered by user actions on the UI or by updates to data
  on the server.
* After an action has moved through the system, the application will be in a new state.
* In Flux applications, **only one action can move through the system
at a time**.

## How do we represent Actions in Code?

At base, actions are just simple JavaScript objects consisting of a property
identifying the action _type_, along with any other data necessary to carry
out the action. That's it.

The following is a basic example of an action that you might see in a ToDo App:

```javascript
{
  actionType: 'ADD_TODO',
  payload: {
    text: 'Write a ToDo App using Flux'
  }
}
```
The `actionType` property (sometimes also just called `type`) identifies the type
of action we are dealing with. The importance of defining the type will be come
clearer later, but for now just keep in mind that if the program can't identify
the type of an action it can't know how to respond. Then also we have a `payload`
property. As the name suggests, this property holds data, the payload as it were, that is
being delivered along with the action. Not all actions will require a payload,
but a great many do. Nothing in the Flux system requires that you name your action
properties in this way, but these are common naming conventions.

## Dispatching Actions

So now that we know that an action is just a JavaScript object, you may be wondering
how actions move through a Flux system. This is a very good question, and although
we will cover it again, we can illustrate the basic procedure here. It's quite simple!

The first step of course is to dispatch an action. Here we make use of a function
exposed on all Flux dispatchers: `dispatch`. The usage is simple. To dispatch an
action, we'd just do this:

```javascript
Dispatcher.dispatch({
  actionType: 'ADD_TODO',
  payload: { text: 'Write a ToDO App using Flux' }
});
```

At this point, the Dispatcher will have queued our action. The next step is for
it to be sent to the stores. Here is where our action's `actionType` property
becomes so important. Each Store in a Flux Application registers itself
with the Dispatcher, providing a callback. Within that callback, each store specifies
the `actionTypes` to which it responds. We won't go into this in detail now,
but the code is usually handled with a simple, efficient, switch statement.
Something like this:

```javascript
switch (action.actionType) {
  case 'ADD_TODO':
    // Call appropriate Store method(s) to handle action.
}
```

## Patterns for Actions

Now that we have a rough outline for how actions move through a Flux application,
let's look at some of the different ways that people manage and create actions. There
are a few different patterns that have developed over time, and you will likely 
encounter a range of practices.

#### The Most Basic (Bad) Pattern

First, though, let's talk about a pattern that is generally **not** used. Probably, the
most basic way to deal with actions would be just to generate an action object
in-place, wherever you need to dispatch, i.e. precisely what we did in the simple
example above:

```javascript
Dispatcher.dispatch({
  actionType: 'ADD_TODO',
  payload: { text: 'Write a ToDO App using Flux' }
});
```

However, this is not a good practice. Who wants to repeatedly write out JavaScript
action objects, when we don't have to? Also, what if our actions change somehow, 
and we need to update our code? We'd have to update everywhere! We can definitely improve on this.

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
So what do we have here? Well it's pretty simple. The function in this case is
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
What are the benefits of this approach? They are multiple. We no longer need to
write out a JavaScript action object each time, and because of that
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
    actionType: ToDoActions.ADD_TODO
  });
};

module.exports = {
  'addToDo': addToDo
};
```

## Resources

- Facebook React Blog, [Flux Actions and the Dispatcher](https://facebook.github.io/react/blog/2014/07/30/flux-actions-and-the-dispatcher.html#actions-and-actioncreators)
- Facebook Flux Documentation, [Actions and Action Creators](https://facebook.github.io/flux/docs/actions-and-the-dispatcher.html#actions-and-action-creators)
