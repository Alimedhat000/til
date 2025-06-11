The `motion` component is the core component for animation in framer motion.

There's a `motion` component for every HTML and SVG elements, for instance 
```js
<motion.div />
<motion.circle />
```

Think of it as a normal React component — but with animation superpowers.

### Custom Components with `motion`

if you want to use a custom React component with motion you can supercharge it with `motion.create()`.

```jsx
const MotionComponent = motion.create(Component)
```

>⚠️ **Important:** Don’t call `motion.create()` inside a React render function. Doing so will recreate the component on every render and break animations.

It's also possible to pass strings to `motion.create`, which will create custom DOM elements.

```jsx
// Will render <custom-element /> into HTML
const MotionComponent = motion.create('custom-element')
```


### Props

`motion` components accept the following props.

##### `initial`

Sets the initial visual state when the component first renders.
  
```jsx
<motion.section initial={{ opacity: 0, x: 0 }} />
```

##### `animate`
  
Defines the target visual state to animate to.
  
```jsx
<motion.section
	initial={{ rotate: "0deg" }}
	animate={{ rotate: "100deg" }} />
```
This will do a spin every time the element is loaded

you can pass an array of values to the animation value to make keyframes

```jsx
<motion.section
	initial={{ rotate: "0deg" }}
	animate={{ rotate: ["10deg", "30deg", "50deg", "100deg"] }} />
```


##### `transition`

Controls the timing and behavior of the animation (e.g., duration, easing, damping, type).

```jsx
<motion.div
  animate={{ x: 100 }}
  transition={{ duration: 0.5, ease: "easeInOut"}}
/>
```

if the animate has multiple keyframes we can use `times` to specify at which percentages these keyframes fire

```jsx
<motion.section
	initial={{ rotate: "0deg" }}
	animate={{ rotate: ["10deg", "30deg", "50deg", "100deg"] }} 
	transition={{ duration: 0.5, ease: "easeInOut",
				times: [0, 0.25, 0.5, 1]}}
	/>
``` 

##### `exit`

Defines the animation state when a component **leaves** the DOM i.e hides/gets removed (usually used with `AnimatePresence`).

```jsx
<motion.div
  initial={{ opacity: 0 }}
  animate={{ opacity: 1 }}
  exit={{ opacity: 0 }}
/>
```

Use this in combination with `AnimatePresence` to animate component unmounts:

```jsx
<AnimatePresence>
  {isVisible && (
    <motion.div
      initial={{ y: 100 }}
      animate={{ y: 0 }}
      exit={{ y: 100 }}
    />
  )}
</AnimatePresence>
```


##### `whileHover` / `whileTap`

These props allow you to define animations based on user **interactions** like hovering or tapping (clicking/touching).

```jsx
<motion.button   whileHover={{ scale: 1.1 }} >   Hover me </motion.button>
```

```jsx
<motion.button   whileTap={{ scale: 0.9 }} >   Tap me </motion.button>
```