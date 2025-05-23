
### Data transfer in React

In React, data is transferred from parent components to child components via props. This data transfer is unidirectional, meaning it flows in only one direction. Any changes made to this data will only affect child components using the data, and not parent or sibling components. This restriction on the flow of data gives us more explicit control over it, resulting in fewer errors in our application.

### Using props in React

Now that we know _how_ data transfers between components, let’s explore _why_ this might be a useful feature in React. Consider the following `Button` component, which then gets rendered multiple times within our `App` component.

```jsx
function Button() {
  return (
    <button>Click Me!</button>
  );
}

export default function App() {
  return (
    <div>
      <Button />
      <Button />
      <Button />
    </div>
  );
}
```

So far so good right? We have a beautiful reusable button that we can use as many times as we like, there is just one small problem.

What if we wanted the text within our second button to be “Don’t Click Me!”? Right now, we would have to create a second button component with this different text.

```jsx
function Button() {
  return (
    <button>Click Me!</button>
  );
}

function Button2() {
  return (
    <button>Don't Click Me!</button>
  );
}

export default function App() {
  return (
    <div>
      <Button />
      <Button2 />
      <Button />
    </div>
  );
}
```

This may not seem like a huge deal right now, but what if we had 10 buttons, each one having different text, fonts, colors, sizes, and any other variation you can think of. Creating a new component for each of these button variations would very quickly lead to a LOT of code duplication.

Let’s see how by using props, we can account for any number of variations with a _single_ button component.

```jsx
function Button(props) {
  const buttonStyle = {
    color: props.color,
    fontSize: props.fontSize + 'px'
  };

  return (
    <button style={buttonStyle}>{props.text}</button>
  );
}

export default function App() {
  return (
    <div>
      <Button text="Click Me!" color="blue" fontSize={12} />
      <Button text="Don't Click Me!" color="red" fontSize={12} />
      <Button text="Click Me!" color="blue" fontSize={20} />
    </div>
  );
}
```

There are a few things going on here.

- The `Button` functional component now receives `props` as a function argument. The individual properties are then referenced within the component via `props.propertyName`.
- When rendering the `Button` components within `App`, the `prop` values are defined on each component.
- Inline styles are dynamically generated and then applied to the `button` element.

### Prop destructuring

A very common pattern you will come across in React is prop [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment). Unpacking your props in the component arguments allows for more concise and readable code. Check out prop destructuring in action in the example below.

```jsx
function Button({ text, color, fontSize }) {
  const buttonStyle = {
    color: color,
    fontSize: fontSize + "px"
  };

  return <button style={buttonStyle}>{text}</button>;
}

export default function App() {
  return (
    <div>
      <Button text="Click Me!" color="blue" fontSize={12} />
      <Button text="Don't Click Me!" color="red" fontSize={12} />
      <Button text="Click Me!" color="blue" fontSize={20} />
    </div>
  );
}
```

### Default props

You may have noticed in the above examples that there is some repetition when defining props on the `Button` components within `App`. In order to stop repeating ourselves re-defining these common values, and to protect our application from undefined values, we can define default parameters to set default values for props.

```jsx
function Button({ text = "Click Me!", color = "blue", fontSize = 12 }) {
  const buttonStyle = {
    color: color,
    fontSize: fontSize + "px"
  };

  return <button style={buttonStyle}>{text}</button>;
}

export default function App() {
  return (
    <div>
      <Button />
      <Button text="Don't Click Me!" color="red" />
      <Button fontSize={20} />
    </div>
  );
}
```

As you can see, we now only need to supply prop values to `Button` when rendering within `App` if they differ from the default values defined in the function parameters.

You may also come across the use of `defaultProps` in some codebases. This was traditionally used to set default values for props, particularly in class components. Here’s how it looks:

```jsx
function Button({ text, color, fontSize }) {
  const buttonStyle = {
    color: color,
    fontSize: fontSize + "px"
  };

  return <button style={buttonStyle}>{text}</button>;
}

Button.defaultProps = {
  text: "Click Me!",
  color: "blue",
  fontSize: 12
};

export default function App() {
  return (
    <div>
      <Button />
      <Button text="Don't Click Me!" color="red" />
      <Button fontSize={20} />
    </div>
  );
}
```

While React now prefers the default parameter approach for function components, understanding `defaultProps` is still useful, especially when working with class components or older codebases.

### Functions as props

In addition to passing variables through to child components as props, you can also pass through functions. Consider the following example.

```jsx
function Button({ text = "Click Me!", color = "blue", fontSize = 12, handleClick }) {
  const buttonStyle = {
    color: color,
    fontSize: fontSize + "px"
  };

  return (
    <button onClick={handleClick} style={buttonStyle}>
      {text}
    </button>
  );
}

export default function App() {
  const handleButtonClick = () => {
    window.location.href = "https://www.google.com";
  };

  return (
    <div>
      <Button handleClick={handleButtonClick} />
    </div>
  );
}
```

- The function `handleButtonClick` is defined in the parent component.
- A reference to this function is passed through as the value for the `handleClick` prop on the `Button` component.
- The function is received in `Button` and is called on a click event.

There are a few things to note here.

- We only pass through a reference to `handleButtonClick`, i.e. we do not include parenthesis when passing the function to `Button`. If we were to do something like `handleClick={handleButtonClick()}` then the function would be called as the button renders.
- Every `Button` calling this function will navigate to the same page. We can refactor the function and supply an argument within `Button` to customize this functionality.

```jsx
function Button({ text = "Click Me!", color = "blue", fontSize = 12, handleClick }) {
  const buttonStyle = {
    color: color,
    fontSize: fontSize + "px"
  };

  return (
    <button onClick={handleClick} style={buttonStyle}>
      {text}
    </button>
  );
}

export default function App() {
  const handleButtonClick = (url) => {
    window.location.href = url;
  };

  return (
    <div>
      <Button handleClick={() => handleButtonClick('www.theodinproject.com')} />
    </div>
  );
}
```

When supplying an argument to the function we can’t just write `onClick={handleClick('www.theodinproject.com')}`, and instead must attach a reference to an anonymous function which then calls the function with the argument. Like the previous example, this is to prevent the function being called during the render.