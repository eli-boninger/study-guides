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

Rules of thumb for deciding if something should in a redux store:

- do other parts of the app care about this data?
- do you need to create further derived data from the original data?
- is the same data driving multiple components?
- is there value to using time-travel debugging with this state?
- do you want to cache the data?
- do you want to keep the data consistent while hot-reloading UI components which may lose their internal state when swapped?

As such, most form state should not be kept in Redux.

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

Reasons for these rules:

- code is predictable when a function's output depends only on its arguments
- bugs are common when functions have side effects
- some of the Redux DevTools depend on these rules being followed

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
- These UI elements check the part of the store they are interested in for changes. If there is a change, re-render

### Utilities

`configureStore` - sets up a store with the provided reducers, along with some default middleware

```
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from '../features/counter/counterSlice'

export default configureStore({
  reducer: {
    counter: counterReducer
  }
})
```

`createSlice` - generates action type strings, action creator functions, and action objects, as well as allows you to write reducer code in way that looks mutable but is not, thanks to `immer`. Example usage for a counter:

```
import { createSlice } from '@reduxjs/toolkit'

export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {
      // Redux Toolkit allows us to write "mutating" logic in reducers. It
      // doesn't actually mutate the state because it uses the immer library,
      // which detects changes to a "draft state" and produces a brand new
      // immutable state based off those changes
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    }
  }
})

export const { increment, decrement, incrementByAmount } = counterSlice.actions

export default counterSlice.reducer
```

`useSelector` - given some function in the slide file such as `export const selectCount = state => state.counter.value`, we can use the selector to get the value in a component with `const count = useSelector(selectCount)`. Our selector functions could also be defined inline:

```
const countPlusTwo = useSelector(state => state.counter.value + 2)
```

The value from `useSelector` will be recalculated each time the store is updated.

`useDispatch` - gives access to the dispatch method: `const dispatch = useDispatch()`

`Provider` - provides the store to the App:

```
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'
import App from './App'
import store from './app/store'
import { Provider } from 'react-redux'
import * as serviceWorker from './serviceWorker'

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

### Async logic with Thunks

A thunk is a kind of Redux function with async logic. Written with two JS functions:

- An inside thunk function, which takes `dispatch` and `getState` as args
- Outside creator function, which creates and returns the thunk

Example:

```
// The function below is called a thunk and allows us to perform async logic.
// It can be dispatched like a regular action: `dispatch(incrementAsync(10))`.
// This will call the thunk with the `dispatch` function as the first argument.
// Async code can then be executed and other actions can be dispatched
export const incrementAsync = amount => dispatch => {
  setTimeout(() => {
    dispatch(incrementByAmount(amount))
  }, 1000)
}
```

Thunks require the `redux-thunk` middleware - this is including already by `configureStore`.

### Putting it all in a React component

```
import React, { useState } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import {
    decrement,
    increment,
    incrementByAmount,
    incrementAsync,
    selectCount
} from './counterSlice'
import styles from './Counter.module.css'

export function Counter() {
    const count = useSelector(selectCount)
    const dispatch = useDispatch()
    const [incrementAmount, setIncrementAmount] = useState('2')

return (
    <div>
        <div className={styles.row}>
            <button
                className={styles.button}
                aria-label="Increment value"
                onClick={() => dispatch(increment())} > +
            </button>
            <span className={styles.value}>{count}</span>
            <button
                className={styles.button}
                aria-label="Decrement value"
                onClick={() => dispatch(decrement())} > -
            </button>
        </div>
        {/_ omit additional rendering output here _/}
    </div>
)
}
```
