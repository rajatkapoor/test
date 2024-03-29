---
title: Writing my first custom react hook - useOutsideClick
slug: writing-my-first-custom-react-hook-useoutsideclick
coverImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1620657605607/RarlGMNI_.png
---
When react hooks were launched, they completely changed the react ecosystem. I have been using react hooks for quite some time now and I am a big fan. But like a lot of other developers, I have never written a custom react hook. This is mainly because firstly, all the functionality I need is available in a third-party hooks library, and secondly, procrastination.

I am a firm believer in learning by doing. So I am going to create a very simple hook - **useOutsideClick**. This hook will help us to trigger a function when a user clicks outside a component.

## Where can we use this?
1. Close expanded states of a component when a user clicks outside
2. Close modals when users click outside the modal

and many more

## How will we create this?
This may not be the best way, but I have been using a very simple approach in my older class-based components. I will just try to replicate that with a custom hook. Here's what we will do:

1. We will add an `onClickListener` to the `document` when the component mounts
2. In this click listener, we will trigger the `outsideClickHandler` when the target of the click lies outside the desired component

# Let's get started

You can find the final code of this tutorial in this  [github repository](https://github.com/rajatkapoor/useOutsideClick) and a [live working demo here](https://use-outside-click.vercel.app/) 

Let's create a react app and run it using the following commands

```
npx create-react-app useOutsideClick
npm install # to install all dependencies
npm run start # to run the app
```

We'll first create the outside click functionality in a simple functional component and then try to extract it into  a custom hook

Let's edit `src/App.js` to look like:


```
import "./styles.css";

export default function App() {
  return (
    <div className="App">
      <div className="main">Click me</div>
    </div>
  );
}
```

and update the styles in `./styles.css`  to make things slightly less ugly

```
html, body, #root {
  display: grid;
  place-items: center;
  height: 100%;
  width: 100%;
}

.main {
  background: lightskyblue;
  font-size: 2rem;
  width: 20vh;
  height: 10vh;
  display: grid;
  place-items: center;
  border-radius: 40px;
}
```
If you check the browser, you'll see something like this

![frame_safari_dark.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1620575478835/bCHwkGbJd.png)

## Adding outside click functionality

We'll now try to detect when the user has clicked outside the div that says "click me" using the  [useEffect](https://reactjs.org/docs/hooks-effect.html)  and  [useRef](https://reactjs.org/docs/hooks-reference.html#useref) hooks.

We will start by creating a new `ref` for the `<div>` outside which we want to detect clicks
```  
const mainRef = useRef();
```
and pass it as the `ref` prop to the div
```
<div className="main" ref={mainRef}>
```

In our click handler, we will check whether the `event.target` lies inside the target element. We can do that using the  [`contains` function](https://htmldom.dev/check-if-an-element-is-a-descendant-of-another/). For now, we will just log if the click is outside the element
 
```
const onOutsideClick = (e) => {
    const inMain = mainRef.current.contains(e.target);
    const isOutside = !inMain;
    if (isOutside) {
      # call the outside click handler here
      console.log("Clicked ouside");
    }
  };
```

We want to listen to clicks on the whole document as soon as the component mounts or whenever the ref changes. We will do that using the [useEffect](https://reactjs.org/docs/hooks-effect.html) hook.
```
useEffect(() => {
    document.addEventListener("click", onOutsideClick);
    // cleaning up the event listener when the component unmounts
    return () => {
      document.removeEventListener("click", onOutsideClick);
    };
  }, [mainRef]);
```

Our `src/App.js` will now be like:

```
import { useEffect, useRef } from "react";
import "./styles.css";

export default function App() {
  const mainRef = useRef();
  const onOutsideClick = (e) => {
    const inMain = mainRef.current.contains(e.target);
    const isOutside = !inMain;
    if (isOutside) {
      console.log("Clicked ouside");
    }
  };
  useEffect(() => {
    document.addEventListener("click", onOutsideClick);
    return () => {
      console.log("cleanup");
      document.removeEventListener("click", onOutsideClick);
    };
  }, [mainRef]);
  return (
    <div className="App">
      <div className="main" ref={mainRef}>
        Click me
      </div>
    </div>
  );
}
```

That's it. We now just need to extract this functionality in a custom hook.

## Creating a custom hook
Create a new file called `useOutsideClick.js`. We will now copy over the code from our `src/App.js` file to `src/useOutsideClick.js` and update it to accept the `componentRef` and the `outsideClickHandler`

```
# src/useOutsideClick.js

import { useEffect } from "react";

export const useOutsideClick = (componentRef, outsideClickHandler) => {
  const onOutsideClick = (e) => {
    // updated this to use the passed componentRef
    if (!componentRef.current) {
      return;
    }
    const inMain = componentRef.current.contains(e.target);
    const isOutside = !inMain;
    if (isOutside) {
      outsideClickHandler();
    }
  };
  useEffect(() => {
    document.addEventListener("click", onOutsideClick);
    return () => {
      console.log("cleanup");
      document.removeEventListener("click", onOutsideClick);
    };
  }, [componentRef]);
};
```

We will now use this inside our app.

```
#src/App.js

import { useEffect, useRef } from "react";
import "./styles.css";
import { useOutsideClick } from "./useOutsideClick";

export default function App() {
  const mainRef = useRef();
  useOutsideClick(mainRef, () => console.log("Clicked outside"));
  return (
    <div className="App">
      <div className="main" ref={mainRef}>
        Click me
      </div>
    </div>
  );
}
```
And things work perfectly 🎉


## Example
We will now update our app to showcase one of the use cases. When the user clicks the blue `<div>`, we will show more content below it. We will hide this content when the user clicks anywhere outside this button on the screen. We maintain this state in the state variable `expanded` 

```
#src/App.js

import { useEffect, useRef, useState } from "react";
import "./styles.css";
import { useOutsideClick } from "./useOutsideClick";

export default function App() {
  const mainRef = useRef();
  // initially not expanded
  const [expanded, setExpanded] = useState(false);

  // set `expanded` to `false` when clicked outside the <div>
  useOutsideClick(mainRef, () => setExpanded(false));
  return (
    <div className="App">
      // set `expanded` to `true` when this <div> is clicked
      <div className="main" ref={mainRef} onClick={() => setExpanded(true)}>
        Click me
      </div>
      // show more details only when `expanded` is `true`
      {expanded && <div className="more">Lorem ipsum dolor sit amet</div>}
    </div>
  );
}

```

```
/* src/styles.css */

/* add this */
.more {
  text-align: center;
  font-size: 1.2rem;
  background: lightskyblue;
}
```
This is how things look now
![CPT2105092244-1680x939.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1620580483969/4JsOXZgYS.gif)

### Summary

Hooray! We have written our first custom hook. You could also check out one of the widely used custom hook libraries( [react-use](https://streamich.github.io/react-use/)  or  [rooks](https://react-hooks.org/) ) and try to recreate one of the hooks for practice
    