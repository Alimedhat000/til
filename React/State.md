#### The useState hook

The `useState` hook is a built-in hook (we’ll talk about hooks later) in React that allows you to define state in a functional component. It takes an initial value as a parameter and returns an array with two elements that we can destructure to get:

1. The current state value
2. A function to update the state value

State definition with `useState` commonly follows this pattern:

```javascript
const [stateValue, setStateValue] = useState(initialValue);

// adapted for our use case:
const [backgroundColor, setBackgroundColor] = useState(initialColor);
```

Even without much knowledge of React, you can, to some extent, understand what’s going on. The `backgroundColor` state is defined with the hook. Then on every button, we set up a _click_ event handler that calls the `setBackgroundColor` function with the corresponding value. Then, magically the new color is applied to the background.


### How does state work in React?

Let’s hit you with some theory.

In React, when a component’s state or props change, the component is destroyed and recreated from scratch. Yes, you heard that right: _destroyed_. This includes the variables, functions, and React nodes. The entire component is recreated but this time the latest state value will be returned from `useState`. This process is called rerendering. Rerendering is a key feature of React that enables it to efficiently update the user interface in response to changes in the underlying data.


#### React reconciliation algorithm

The process of rerendering generates a new virtual DOM (Document Object Model) tree. The virtual DOM is a lightweight representation of the actual DOM that React uses to keep track of the current state of the UI. React then compares the new virtual DOM tree to the previous one and calculates the minimal set of changes needed to update the actual DOM. This is the reconciliation algorithm.