## React interview prep

Redux is a pattern and library for managing and updating application state, using events called "actions". It serves as a centralized store for state that needs to be used across your entire application, with rules ensuring that the state can only be updated in a predictable fashion.

### When to use and when not to use, tradeoffs

#### When should you use Redux?

- Large amounts of application state needed in many places in an app
- The app state is updated frequently over time
- The logic to update app state may be complex
- The app is medium to large and worked on by several devs
- You need to see how the state is being updated over time
- You have a plan to benefit from what redux offers

#### When shouldn't you use Redux?

- If the only thing you need to do is avoid passing data as props through 15 layers of components, that's what React context is for
- If you just need to cache some server data
- "Don't use redux until you have problems with vanilla React

Redux is a tradeoff. It asks you to:

- Describe application state as plain objects and arrays
- Describe changes in the system as plain objects
- Describe logic for handling changes as pure functions

The tradeoff that Redux offers is to add indirection to decouple “what happened” from “how things change”.

### Parts of the package

Redux is a standalone JS library that can integrate with any framework. Commonly used with

- React-redux: let's your React components interact with a Redux store
- Redux toolkit: the recommended approach for writing Redux logic. Contains packages and functions essential to building a Redux app. Simplifies tasks, prevents common mistakes, makes writing the app easier.
- Redux DevTools Extension: classic dev tool, inspect store and changes over time

### Terms & concepts

Parts of a stateful app:

- state: the source of truth
- view: description of the UI based on the state
- actions: events that occur based on user input or otherwise that change the state

This works in React's unidirectional data flow:

1. state describes point in time
2. UI renders based on that state
3. action occurs to alter state
4. UI re-renders ... and so on

Sometimes this simplicity breaks down when multiple components need access to the same state. Redux helps to move this logic out of the views to centralize state and define interactions with state in a easily debuggable way.

**Redux expects all state changes to be done immutably.**

#### Terms

##### Action

A plain JS object with a `type` field. Type is string general in the form `"domain/eventName"`. Actions can also have a `payload`

```
const addTodoAction = {
  type: 'todos/todoAdded',
  payload: 'Buy milk'
}
```

##### Action Creator:

A function that creates and returns an action object

```
const addTodo = text => {
  return {
    type: 'todos/todoAdded',
    payload: text
  }
}
```

##### Reducer

A function that receives the current `state` and `action` object, decides how to update the state if needed, and returns the new state (`(state, action) => newState`). Similar to an event listener.

Rules of reducers:

- only calculate the new state based on the `state` and `action` arguments
- cannot modify existing state, must make immutable updates
- no async logic, no random value calcs, no side effects

Logic in reducers typically:

- Checks to see if the reducer care about the received action
  - if so, copy the state, update the copy, and return it
  - if not, return the initial state unchanged

```
const initialState = { value: 0 }

function counterReducer(state = initialState, action) {
  // Check to see if the reducer cares about this action
  if (action.type === 'counter/increment') {
    // If so, make a copy of `state`
    return {
      ...state,
      // and update the copy with the new value
      value: state.value + 1
    }
  }
  // otherwise return the existing state unchanged
  return state
}
```

If/else logic, switch statements, are all okay in reducers.

#### Store

Living place of the current `state`. Created by passing in a reducer. `getState` method returns the current state value.

```
import { configureStore } from '@reduxjs/toolkit'

const store = configureStore({ reducer: counterReducer })

console.log(store.getState())
// {value: 0}
```

#### Dispatch

Method to trigger an action on a store (`store.dispatch({ type: 'counter/increment' })`). Will run the store's reduce with the action. Typically an action creator is called to dispatch the right action:

```
const increment = () => {
  return {
    type: 'counter/increment'
  }
}

store.dispatch(increment())

console.log(store.getState())
// {value: 2}
```

#### Selector

Functions that know how to select specific components of `state`. Helps avoid repeated logic as store size increases.

```
const selectCounterValue = state => state.value

const currentValue = selectCounterValue(store.getState())
console.log(currentValue)
// 2
```

#### Redux application data flow

##### Initial setup

- A `store` is creating using a root reducer function
- The `store` calls the root reducer once, and saves the return value in `state`
- When the UI is first rendered, components access current state and decide what to render. They subscribe to chnages in the store.

##### Updates

- Something happens in app
- Action dispatched to the store
- Reducer runs based on the action, updates store as required
- All parts of UI that are subscribed to the store are notified of the update
- These UI elements check the part of the store they are interested in for changes. If there is a change, rerender
