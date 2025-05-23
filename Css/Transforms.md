### [Introduction](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#introduction)

The `transform` property is a powerful tool to change the appearance of elements without affecting the natural document flow.

You have likely seen it in action on many of your favorite websites! Transforms are very commonly used for animated effects. While we are sure you’ll like to create sleek animations of your own, we first need to understand how transforms work.

### [Lesson overview](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#lesson-overview)

This section contains a general overview of topics that you will learn in this lesson.

- How to use 2D transforms.
- How to use 3D transforms.
- How to chain multiple transforms.
- The benefits of using the `transform` property.

### [Basics of transforms](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#basics-of-transforms)

The `transform` property takes in one or more CSS transform functions as its values, with those functions taking in their own value, usually an angle or a number.

Almost all elements can have the `transform` property applied to it, with the exceptions being `<col>`, `<colgroup>`, and non-replaced inline elements. “Non-replaced” refers to elements whose content is contained within the HTML document (`<span>`, `<b>`, and `<em>`, for example), as opposed to a “replaced” element’s content being contained outside of the document (`<a>`, `<iframe>`, and `<img>`, for example). You do not need to memorize every element that is non-replaced, but rather keep this knowledge in mind should you try to apply the `transform` property to an element and aren’t sure why it isn’t working.

### [Two-dimensional transforms](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#two-dimensional-transforms)

In this section, we’ll go through 2D transforms with the following transform functions: `rotate`, `scale`, `skew`, and `translate`.

#### [Rotate](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#rotate)

This is the transform function value to rotate an element on a 2D plane:

```css
.element {
  transform: rotate();
}
```

Copy

Below is a CodePen that shows how the value affects the target element.

#### [Scale](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#scale)

These are the transform function values to scale an element on a 2D plane:

```css
.element {
  transform: scaleX();
  transform: scaleY();
  transform: scale();
}
```

Copy

Below is a CodePen that shows how each value affects the target element.

#### [Skew](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#skew)

These are the transform function values to skew an element on a 2D plane:

```css
.element {
  transform: skewX();
  transform: skewY();
  transform: skew();
}
```

Copy

Below is a CodePen that shows how each value affects the target element.

#### [Translate](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#translate)

These are the transform function values to translate an element on a 2D plane:

```css
.element {
  transform: translateX();
  transform: translateY();
  transform: translate();
}
```

Copy

Below is a CodePen that shows how each value affects the target element.

### [Chaining multiple transforms](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#chaining-multiple-transforms)

Now that you have a grasp of 2D transforms, we will learn how to chain them. Chaining multiple transforms is done by adding more transform functions with a space between each one. Take a look at the code below:

```html
<div class="red-box"></div>
<div class="blue-box"></div>
```

Copy

```css
.red-box,
.blue-box {
  position: absolute;
  width: 100px;
  height: 100px;
}

.red-box {
  background: red;
  transform: rotate(45deg) translate(200%);
}

.blue-box {
  background: blue;
  transform: translate(200%) rotate(45deg);
}
```

Copy

There are two boxes located at the same position. We chained `rotate` and `translate` function values to both boxes, but in different orders. Make a guess on what happens to each box, then click the “Result” link in the Codepen below to see if you were right.

If you guessed correctly, congratulations! But this is a tricky concept. MDN’s transform docs state that “[composite transforms are effectively applied in order from right to left](https://developer.mozilla.org/en-US/docs/Web/CSS/transform#values)”.

The blue box rotates 45 degrees on the spot, then translates on the X axis by 200%, moving it directly to the right. The red box translates by 200% first, so moves to the right, but the transform origin is still where it used to be. Therefore, it rotates 45 degrees around that original point, making the red box “swing down” to end up diagonally from where it started.

While you can generally chain multiple transforms in any order for various results, there is one exception: `perspective`. This brings us nicely to the next section where `perspective` is involved.

### [Three-dimensional transforms](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#three-dimensional-transforms)

The `rotate`, `scale`, and `translate` transform functions aren’t limited to just 2D planes. They also work for 3D planes as well! However, to perceive a 3D effect on some of these function values, `perspective` is required.

From here on, the examples get more complicated. Feel free to play around with these properties, but be careful not to get too sidetracked with them.

#### [Perspective](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#perspective)

This is the transform function value to set the distance from the user to the z = 0 plane:

```css
.element {
  transform: perspective();
}
```

Copy

Essentially, by setting a `perspective` value, we are telling the object to render as if we were viewing it from a specific distance on the z-axis.

Unlike other transform function values, `perspective` must be declared first (leftmost) when there are multiple transform function values. In the upcoming examples for `rotate`, `scale`, and `translate`, we will be able to see how it affects the target element.

#### [Rotate specific axis](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#rotate-specific-axis)

These are the additional transform function values to rotate an element in a 3D space:

```css
.element {
  transform: rotateX();
  transform: rotateY();
  transform: rotateZ();
  transform: rotate3d();
}
```

Copy

Below is a CodePen that shows how the first three values affects the target element.

#### [Scale specific axis](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#scale-specific-axis)

These are the additional transform function values to scale an element in a 3D space:

```css
.element {
  transform: scaleZ();
  transform: scale3d();
}
```

Copy

See MDN’s 3D cube in action with [`scaleZ`](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/scaleZ\(\)) and [`scale3d`](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/scale3d\(\)).

#### [Translate specific axis](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#translate-specific-axis)

These are the additional transform function values to translate an element in a 3D space:

```css
.element {
  transform: translateZ();
  transform: translate3d();
}
```

Copy

`translateZ` doesn’t do much without `perspective`. Instead, `perspective` and `translateZ` work together to create the illusion of 3-dimensional distance from the rendered object, as shown in the example below.

#### [Matrix](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#matrix)

While not strictly a 3D transform function, matrix is mentioned last in this lesson due to how uncommonly used it is. These are the transform function values for it.

```css
.element {
  transform: matrix();
  transform: matrix3d();
}
```

Copy

Matrix is a way of combining all transform functions into one. It is seldom used due to its poor readability, and almost never written by hand. Unless you have a very complex transformation to apply, you should use other transform function values instead.

It is enough for you to know _that_ these functions exist and generally how they work. However, it is not important for you to feel comfortable building with them.

### [Benefits of transforms](https://www.theodinproject.com/lessons/node-path-advanced-html-and-css-transforms#benefits-of-transforms)

In order to understand why the `transform` property is great, you have to be aware of CSS triggers. You can learn about it in [The Pixel Pipeline](https://developers.google.com/web/fundamentals/performance/rendering/#the_pixel_pipeline) section from Google’s Web Fundamentals.

The key benefit of using `transform` is that it occurs during **composition**. This makes it cheaper to use compared to many other CSS properties. You can see what triggers are executed with each CSS property in this [table of CSS triggers](https://web.archive.org/web/20220727225220/https://csstriggers.com/).