## React interview prep

### Features of react

1. JSX - syntax extension to JavaScript. Can write HTML structures in the same file as JS code
2. Components - building blocks of React. Split user interface into independent, reusable parts
3. Virtual DOM - lightweight representation of the real DOM in memory. When the state of an object changes, the virtual DOM changes only that object in the real DOM, rather than updating all the objects
4. One-way data binding - Unidirectional data flow with nesting components
5. Performance - Updates only components that have changed, rather than all components.
6. Supports SSR - this boosts SEO of an app

### Common interview questions

1. Can browsers read JSX directly?

Nope, needs transpiliation, via babel or otherwise

2. What is the virtual DOM?

Lightweight representation of the DOM which React stores in memory.

3. Why use React?

Simpler structure with reusable components. Performant due to the many optimizations with the virtual DOM. Easy to debug due to the unidirectional data flow. Good debugging tools.

4. What is an event in React?

Key press, mouse click, etc. In React you pass a function as the event handler, as in `<Button onClick={handleClick}>`.

5. What is a synthetic event in React?

Combination of various browsers' native events in a single API to keep event responses consistent across browsers. Ex: `preventDefault()`

6. Why are `key`s required in React lists?

Allows react to keep track of changes, updates, and deletions for particular list items to avoid unnecessary rerenders.

7. What are the differences between `state` and `props` and how they are used?

State is internal to a component. For example a checkbox component might use state to keep track of whether it is checked or unchecked. No other components access this state directly. Props are passed between components. If another component needs to know about the checked state, this can be passed to the checkbox's children via props.

Props are readonly and should not be mutated. State is meant to be updated.
