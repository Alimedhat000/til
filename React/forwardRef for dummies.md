`forwardRef` is a React utility that lets a parent component pass a `ref` through a child component to access the child’s DOM node or instance directly. This is super helpful when the child is wrapped by other components and doesn’t expose the ref by default.

But what does that really mean? 

As you know —or should know— `ref` is simply a way to **control DOM elements directly** in React.

For example, you can:

- Focus an input: `inputRef.current.focus()`
- Scroll to an element: `divRef.current.scrollIntoView()`
- Measure size or position: `divRef.current.getBoundingClientRect()`

But here’s the **problem**:  
If you wrap an element inside your own component, like this:
```jsx
export const FancyButton = (props) => {
  return (
	<button className="fancy">
	  {props.children}
	</button>
  );
}
```

And then you try this:
```jsx
const btnRef = useRef();
<FancyButton ref={btnRef}>Click me</FancyButton>

```

You expect `btnRef.current` to be the `<button>`, but it’s actually `null`.  
Why? Because **React doesn't pass `ref` as a regular prop.** It’s special — it has to be explicitly handled.


And **This is where `forwardRef` saves the day:**
```jsx 
const FancyButton = React.forwardRef((props, ref) => {
  return <button ref={ref} className="fancy">{props.children}</button>;
});
```

Now, `ref` **flows through** your component and lands on the actual `<button>` — giving you back control.