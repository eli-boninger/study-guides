## React interview prep

### Features of react

1. JSX - syntax extension to JavaScript. Can write HTML structures in the same file as JS code
2. Components - building blocks of React. Split user interface into independent, reusable parts
3. Virtual DOM - lightweight representation of the real DOM in memory. When the state of an object changes, the virtual DOM changes only that object in the real DOM, rather than updating all the objects
4. One-way data binding - Unidirectional data flow with nesting components
5. Performance - Updates only components that have changed, rather than all components.
6. Supports SSR - this boosts SEO of an app

### Notes on React straight from the docs

Basic knowledge of React is assumed in these notes. These are meant to capture things experienced developers may have missed or never learned.

#### Describing the UI

JSX is a syntax extension. It is not part of React but is frequently used together with React.

The `<></>` (read: empty) tag is also called a Fragment. It leaves no trace in the HTML tree. To generate several DOM nodes when mapping over a list, use `<Fragment>` in order to pass a key (`<></>` cannot accept a key).

Though most attributes are camelCase, `aria-*` and `data-*` attributes use dashes as in HTML.

When using default props in a component (`const MyComponent = ({ num = 3 }) => ...`), the default will only be used if the passed-in prop is `undefined`. If `num` is passed in as `null` or `0`, those values will be used, not `3`.

When using `x && y` for conditional rendering, `x` should never be a number. The logical and operator will evaluates from left to right, returning either the first falsy operand, or, if all are true, the final operand on the right. Because 0 is falsy, if `x` is zero then 0 will be returned and rendered in the React DOM. As such, `{arr.length && <MyComponent />}` should never be used.

##### Purity

What is a pure function?

1. A pure function doesn't change any objects or variables that existed before it was called.
2. Deterministic - given the same input it should always return the same result.

React assumes the every component is a pure function. Given the same props, state, and context, a React component should always return the same JSX.

React's StrictMode calls all component functions twice to check for purity.

Event handlers don't need to be pure because they are not run during rendering.

#### Adding interactivity

Event handlers should start with `handle` (according to convention in the React docs). In particular, if the event is `onEventName` then the handle should be named `handleEventName`.

Events bubble up. If a div contains a button, both of which have an `onClick` handler, then pressing that button would trigger both, from child to parent. This is true for all events except `onScroll`. To prevent this, the child can call `e.stopPropagation()`.

Adding `Capture` to the end of an event (e.g. `onClickCapture`) will catch events on all children, even if they stopped propagation. This is good for analytics.

##### Triggering a render

Two reasons to render:

- It is my initial render (this is done via `createRoot`)
- My or my ancestors' state has changed (re-render)

Re-renders will happen recursively, traversing all nested components within the component that triggered the re-render

On first commit to the DOM, React uses the DOM `appendChild()` method. Subsequently, React makes only the necessary DOM changes. React only changes the DOM if there is a difference between renders.

---

React waits until all code in event handlers is complete before processing state updates (batching).

#### Hooks

Hooks are special functions that are only available while React is rendering.

Rules of hooks:

1. Only callable at top level
2. No calling within loops or conditionals

##### useState

Call `useState` with the initial value for the state variable. If a function is based in as the initial value, it will be treated as an _initializer_ funtion. It should be pure, take no args, and return a value (any type). When initializing the component, React will call the function and use the result as the initial value.

Similar to an initializer, you can also pass a function to the set function (e.g. the `setValue` in `const [value, setValue] = useState(...)`). This is considered an _updater_ function. It must be pure, should take the pending state as its only argument, and should return the next state. React will put your updater function in a queue and re-render your component. During the next render, React will calculate the next state by applying all of the queued updaters to the previous state.

When in strict mode, React will call the initializer and/or the updater function twice to ensure it is pure.

Some trickiness:

- The `set` function for `useState` only updates the value for the _next_ render. If you read it directly after calling `set` it will not yet be updated.
- If you the new value passed in `set` is the same as the previous one (according to an `Object.is` call) then re-render is skipped
- State updates are batched such that all event handlers responding to the event have run. This is an optimization.

Consider the following two examples, where `age` initially is set to 42:

```
function handleClick() {
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
}

// age is 43 after the above runs
```

```
function handleClick() {
  setAge(a => a + 1); // setAge(42 => 43)
  setAge(a => a + 1); // setAge(43 => 44)
  setAge(a => a + 1); // setAge(44 => 45)
}

// all queued updaters are applied in order, thereby successfully calculating from the previous value
```

Common convention is to name the variable in an updater function the first letter of the state variable in question (e.g. `a` for `age`).

Generally there is no different between something like `setAge(a + 1)` and `setAge(a => a + 1)`, except in a case similar to the above example.

Don't mutate state objects or state arrays directly.

```
const [obj, setObj] = useState({ a: 1, b: 2 });
...

obj.a = 3 // this is bad
setObj({ ...obj, a: 3 }) // this is good
```

Leverage the spread operator as well as functions such as `map` and `filter`.

React saves initial state then ignores it on following renders. As such, the following calls `createInitialTodos()` on every render but ignores it every time except the first.

```
const [todos, setTodos] = useState(createInitialTodos());
```

This is an example of where an initializer function should be used instead.

```
const [todos, setTodos] = useState(createInitialTodos);
```

You can reset a component's state by passing a different key to it.

In rare cases you may need to update a state variable in response to rendering. Try to avoid this.

- If the value can be computed entirely from `props` or other state, remove it
- Try to update the state in event handlers instead

Let's say you need to know the previous value of counter so you can display whether it was most recently increment or decrement. The following pattern will work.

```
export default function CountLabel({ count }) {
  const [prevCount, setPrevCount] = useState(count);
  const [trend, setTrend] = useState(null);
  if (prevCount !== count) {
    setPrevCount(count);
    setTrend(count > prevCount ? 'increasing' : 'decreasing');
  }
  return (
    <>
      <h1>{count}</h1>
      {trend && <p>The count is {trend}</p>}
    </>
  );
}
```

A `set` function called in render must be behind a conditional to avoid an infinite loop. This type of logic can only update the state of the current component.

##### useCallback

### Common interview questions

#### 1. Can browsers read JSX directly?

Nope, needs transpiliation, via babel or otherwise

#### 2. What is the virtual DOM?

Lightweight representation of the DOM which React stores in memory and syncs with the real DOM via a library such as ReactDOM.

For very DOM object there is a corresponding virtual DOM object with the same properties. Changes in the virtual DOM will not reflect on the screen directly.

React uses two virtual DOMs to render the UI, one representing current state and one representing previous state. When state changes, React compares the two and only updates the elements with changes.

#### 3. Why use React?

Simpler structure with reusable components. Performant due to the many optimizations with the virtual DOM. Easy to debug due to the unidirectional data flow. Good debugging tools.

#### 4. What is an event in React?

Key press, mouse click, etc. In React you pass a function as the event handler, as in `<Button onClick={handleClick}>`.

#### 5. What is a synthetic event in React?

Combination of various browsers' native events in a single API to keep event responses consistent across browsers. Ex: `preventDefault()`

#### 6. Why are `key`s required in React lists?

Allows react to keep track of changes, updates, and deletions for particular list items to avoid unnecessary re-renders. Without these, React doesn't understand the order or uniqueness of each element.

#### 7. What are the differences between `state` and `props` and how they are used?

State is internal to a component. For example a checkbox component might use state to keep track of whether it is checked or unchecked. No other components access this state directly. Props are passed between components. If another component needs to know about the checked state, this can be passed to the checkbox's children via props.

Props are readonly and immutable. State is meant to be updated.

#### 8. What is the difference between controlled and uncontrolled components?

In a controlled component, the value is controlld by React. For example, a state value to represent input is updated each time the user enters text in the field.

```
const MyInput = () => {
    const [value, setValue] = useState("")

    return <input value={value} onChange={e => setValue(e.target.value)} />
}
```

With an uncontrolled component, the value of the input is controlled by the DOM itself. React performs no actions when the value of the input changes. We can access the data with a ref.

```
const MyInput = () => {
    const inputRef = useRef(null)

    // retrieve value with inputRef.current.value

    return (
        <input ref={inputRef} />
    )
}
```

#### 9. How do you clean up a `useEffect`?

Not always necessary, but will be required if the component does something such as polling:

```
const PollingComponent = (props) => {
    const { pollingFunc } = props;

    useEffect(() => {
        const interval = setInterval(pollingFunc, 5000)
        return () => clearInterval(interval)
    }, [])

    ...
}
```

#### 10. When is it appropriate to use refs?

- Managing focus, media playback, text selection
- Integrating with third-party DOM libraries
- Triggering imperative animations

#### 11. When do re-renders occur, and how can unnecessary re-renders be avoid?

re-renders occur when props or state of component have changed. re-rendering components that haven't actually been updated should be avoided. We can avoid unnecessary re-renders by:

- using the dependency array in `useEffect`` properly such that it is run as little as possible
- have components do one thing, keep updates as far down in the DOM tree as possible (i.e. some universal state passed down in props to every component is bad)

#### 12. What are some ways to optimize React component performance

- `useMemo`
