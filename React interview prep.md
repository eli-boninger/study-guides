## React interview prep

### Features of react

1. JSX - syntax extension to JavaScript. Can write HTML structures in the same file as JS code
2. Components - building blocks of React. Split user interface into independent, reusable parts
3. Virtual DOM - lightweight representation of the real DOM in memory. When the state of an object changes, the virtual DOM changes only that object in the real DOM, rather than updating all the objects
4. One-way data binding - Unidirectional data flow with nesting components
5. Performance - Updates only components that have changed, rather than all components.

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
