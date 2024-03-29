---
title: 4 ways to perfectly center content using CSS
slug: 4-ways-to-perfectly-center-content-using-css
coverImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1620984017493/vwv-QyFYm.png
---
Centering content in CSS is something that beginners struggle a lot with. CSS provides a lot of options that allow you to center content inside a container. We'll try some of the most commonly used ones.

## Centering text

Let's start by learning how to center text in a container

```
<!--  our html has a container and text content inside -->
<div class="container center">
  this text should be centered
</div>
    
```
We can center the text content using `text-align: center`. Here's is how our CSS looks

```
/* Just to see how big the container is*/
.container {
  background: black;
  font-size: 2rem;
  color: turquoise;
  height: 400px;
}

.center {
  text-align: center;  /* horizontally center aligns the text */

  vertical-align: middle;  /* aligns the text in the middle of the `line-height` of the text*/
}
```
See it in action:

%[https://codepen.io/rajatkapoor/pen/VwpvGMZ]

If you want to place the text at the center of the screen, wrap it in a `div` and follow any of the techniques below.

## Centering a div inside another

There are multiple ways to do it. Check out all of these and let me know your favorite one in the comments. For the next few examples, we'll use this common HTML and CSS and highlight only the CSS changes needed to center the contents

#### HTML:
```
<!--  our html has a container which contains a div we wish to center-->
<div class="container center">
  <div class="inner">
  </div>
</div>
```
 #### CSS:
```
html,
body {
  height: 100%;
}
.container {
  background: black;
  width: 100%;
  height: 100%;
  font-size: 2rem;
}

.inner {
  background: turquoise;
  width: 50px;
  height: 50px;
  border-radius: 50%;
}
```
### Using `margin`

We can horizontally center the inner div using margin

```
.inner{
  margin: auto;
}
```
See it in action here

%[https://codepen.io/rajatkapoor/pen/yLMYQYN]

### Using `flex`
To center this using flex, we need the following CSS:

```
.center{
  display: flex;
  align-items: center;
  justify-content: center;
}
```
See it in action here

%[https://codepen.io/rajatkapoor/pen/qBrOJQR]


### Using `grid`

```
.center{
  display: grid;
  place-items: center;
}
```
See it in action here

%[https://codepen.io/rajatkapoor/pen/RwpWqwy]

## Conclusion

This is not all of it. There are many other ways to center content. Post your favorite way of centering content in the comments.


    