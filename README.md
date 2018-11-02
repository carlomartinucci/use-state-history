# use-state-history

## Motivation

If you use [React Hooks](https://reactjs.org/docs/hooks-intro.html), you may want an easy solution to handle not just the current state with [useState](https://reactjs.org/docs/hooks-state.html), but also the history of the changes to that state, for instance with `undo` and `redo` functionality.

`useStateHistory` is a simple custom hook that gives you exactly that.

## Getting Started

To get it started, add `use-state-history` to your project:

```
npm install --save use-state-history
```

or

```
yarn add use-state-history
```

Please note that `use-state-history` requires `react@^16.7.0-alpha.0` as a peer dependency.

## Examples

### Basic Usage

```jsx
import React from 'react';
import useStateHistory from 'use-state-history'

function Counter() {
  const [count, setCount, { undo, redo }] = useStateHistory(0);

  return <div>
    <h1>Now: {count}</h1>
    <button onClick={() => setCount(count + 1)}> +1 </button>
    <button onClick={() => setCount(count - 1)}> -1 </button>
    <button onClick={() => setCount(0)}> => 0 </button>

    <button onClick={undo} disabled={!undo}> undo </button>
    <button onClick={redo} disabled={!redo}> redo </button>
  </div>;
}
```

### To visualize the history

```jsx
import React from 'react';
import useStateHistory from 'use-state-history'

function Counter() {
  const [count, setCount, { history, index, undo, redo }] = useStateHistory(0);

  return <div>
    <h1>Now: {count}</h1>
    <h2>History: {history.map((historyCount, currentIndex) => <span style={ currentIndex === index ? {color: 'red'} : null}>{historyCount}&nbsp;</span>)}</h2>
    <button onClick={() => setCount(count + 1)}> +1 </button>
    <button onClick={() => setCount(count - 1)}> -1 </button>
    <button onClick={() => setCount(0)}> => 0 </button>

    <button onClick={undo} disabled={!undo}> undo </button>
    <button onClick={redo} disabled={!redo}> redo </button>
  </div>;
}
```

## Implementation

`useStateHistory` is as simple as this:

```js
import { useState } from 'react';

function useStateHistory(initialValue) {
  const [history, setHistory] = useState([initialValue])
  const [index, setIndex] = useState(0)

  const state = history[index]
  const setState = (newState) => {
    setHistory(history => history.slice(0, index + 1).concat(newState))
    setIndex(index => index + 1)
  }
  let undo, redo
  if (index > 0)
    undo = () => setIndex(index => index - 1)
  if (index < history.length - 1)
    redo = () => setIndex(index => index + 1)

  return [state, setState, { history, index, setHistory, setIndex, undo, redo }];
}
```

## License

MIT
