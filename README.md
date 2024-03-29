# chtml-masonry

> A Computed HTML solution to the Masonry layout
> 
> Gary Royal

![screenshot](masonry.png)

## Overview

**Computed HTML** (CHTML) is a programming model in which the tags describing a complex layout are compiled in RAM, then passed to the browser's HTML interpreter to render in a single paint. 

**The Masonry layout** (also known as the Pinterest layout) is a matrix of semi-regular elements separated by a constant margin, like bricks in a wall, but rotated 90 degrees so that it grows from the top down as new elements are added.

These listings demonstrate the Computed HTML model by using it as a runtime for the Masonry layout algorithm described below.

## Features

* Time to Interactive: instant 
* Responsive from 200 to 2000 pixels vw
* Supports high density displays
* Infinite scroll
* Mobile ready

## Quick Start

1. Clone or download the repo
2. Open the chtml-masonry folder
3. Drop the file masonry.html into your browser window

## Computed HTML

Computed HTML is [element.innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) scaled to the page level.

Arbitrarily complex layouts are generated in JavaScript and inserted into a minimal or empty DOM. The tags describing the layout are assembled with their attributes compiled inline. 

Attributes may be drawn from cookies, variables, hardcoded data, XMLHttp requests, or the environment the code is executing in (such as the display geometry).  

### Performance

Sub-second TTIs are typical regardless of page complexity. Scripts are usually more compact than the layouts they describe, and may be cached like any other static asset. Once the browser cache is populated, only ephemeral data need be sent to the client.

Bandwidth reduction and performance improvement is by orders of magnitude compared to traditional SSR / DHTML.

```
hello.html:
...
<body>
   <script src="hello.js">
   <div id="greeting"></div>
</body>

hello.js:
...
document.addEventListener("DOMContentLoaded", function(){

    document.getElementById("greeting").innerHTML = [
        '<p>Hello, World</p>',
    ].join('');  
});
```

## Masonry layout algorithm

```
const margin = 8
const dpr = displayPixelRatio
calc columns per row
calc column width
var img width = (column width - margin)

column height[0 .. columns per row] = margin

for each photo i

    j = index(minimum(column_height))

    left = offset of jth column

    top = column height[j]

    img height = (aspect ratio * img width)

    quality = (dpr>1 && photo[i][width] >= img_width * dpr) ? dpr : 1;

    tile_size = maximum(img_width,img_height) * quality;

    chtml[i] = '<div class=brick style="
        left:(left)px; 
        top:(top)px; 
        width:(img_width)px; 
        height:(img_height)px;
        url(https://picsum.photos/seed/i/tile_size)
    "></div>'

    let column height[j] += img height + margin

next i

gallery.innerHTML += array to string(chtml)
```

#### Computed HTML output element @2x quality

```
chtml[i] = <div class="lozad brick" 
style="top:1384px;left:61px;width:192px;height:144px;background-
image:url('https://picsum.photos/seed/30/384');"></div>
```

## High Definition Displays

By default, thumbnail images (tiles) are fetched at a multiple of the device pixel ratio if `device pixel ratio > 1` and `image width >= (display width * device pixel ratio)`. 

### Splurge mode

There is very little documentation concerning best practices for rendering thumbnail photographs on HD displays, even Retina displays.

Displaying all photos at 1x that aren't at least `display width * device pixel ratio` wide means leaving a lot of pixels on the table that might have been used to improve the rendition of other photos, not just the ones aligned to the device pixel ratio (which varies from one device to the next).

Splurge mode tries to pack as much detail into thumbnails as possible by finding the densest pixel ratio below the device pixel ratio that will fit.

So if the device pixel ratio is 4, but the photo isn't wide enough for a 4x thumbnail, it will consider 3x, and then 2x before defaulting to 1x.

Set the constant `splurge = false` to disable this behavior.

## Lorem Picsum

**[Lorem Picsum](https://picsum.photos/)** is a placeholder service, an API for fetching arbitrary pictures with arbitrary dimensions for demonstration purposes.

It is used in place of distributing an image database with this repo.

`shapefile.js` is a list of picture dimensions extracted from my own database of user-uploaded photos. Those dimensions are applied to the picsum request to download photos at irregular but realistic proportions to demonstrate the masonry effect. 

Picsum placeholders are fetched in 'seed' mode, that is, for an arbitrary seed value, Picsum returns an arbitrary placeholder, subject to the constraint that the same seed will always return the same image.

## Lozad.js

**[Lozad.js](https://github.com/ApoorvSaxena/lozad.js)** is an observer-based lazy loader for images. It is optional, but it allows the layout algorithm to stay ahead of the scroll bar.  

## Working on

A PHP back end for CHTML applications
