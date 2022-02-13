---
title: "Dynamically get image dimensions from image URL in React"
date: 2021-01-14T08:30:00+05:30
author: Prashant Shahi
show_reading_time: true
toc: true
tags:
  - react
  - image-dimension
---

![Image Dimensions](/images/image-dimensions.png)

## Problem statement
There might be cases when you would be required to obtain metadata of a media before it is loaded.
The usual way you would go about it is to have an API that gives back the media metadata from the server. But here we will be getting the image dimension using React.

## Steps to solution
Firstly, we will be creating an instance of the HTML Image element and then pass the image URL.

```jsx
const img = new Image();
img.src = imageUrl;
```

Next, we can access the image dimension using the `#Image.onload` function which will get called when the image is loaded.

```jsx
img.onload = () => {
  console.log(img.height);
  console.log(img.width);
};
```

Now, we can enclose the code above in a function and pass back the dimension using React State hooks.
Introducing the states in the caller function/class:

```jsx
const [imageDimensions, setImageDimensions] = useState({});
```

Inside the called function, we can set image dimensions using `setImageDimensions` to pass them back to the caller.

```jsx
const setImageSize = (setImageDimensions, imageUrl) => {
  const img = new Image();
  img.src = imageUrl;
  img.onload = () => {
    setImageDimensions({
      height: img.height,
      width: img.width
    });
  };
};
```

## Complete Solution
Lastly, you may need to use `useEffect` to run the function once after the rendering.
You can find the complete code here:

```jsx
import React, { useEffect, useState } from "react";

const loadImage = (setImageDimensions, imageUrl) => {
  const img = new Image();
  img.src = imageUrl;

  img.onload = () => {
    setImageDimensions({
      height: img.height,
      width: img.width
    });
  };
  img.onerror = (err) => {
    console.log("img error");
    console.error(err);
  };
};

const App = () => {
  const [imageDimensions, setImageDimensions] = useState({});
  const imageUrl = "https://picsum.photos/200/300";

  useEffect(() => {
    setImageSize(setImageDimensions, imageUrl);
    console.log(imageDimensions);
  }, []);

  return (
    <div>
      {Object.keys(imageDimensions).length === 0 ? (
        <b>Calculating...</b>
      ) : (
        <>
          <p>
            <b>Height:</b> {imageDimensions.height}{" "}
          </p>
          <p>
            <b>Width:</b> {imageDimensions.width}{" "}
          </p>
        </>
      )}
    </div>
  );
};

export default App;
```

---

This article was originally published in https://medium.com/@coolboi567/dynamically-get-image-dimensions-from-image-url-in-react-d7e216887b68.
