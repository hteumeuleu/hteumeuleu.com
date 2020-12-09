---
title:  "Background properties in VML"
---

Here’s how to emulate the CSS properties `background-position` and `background-size` in VML.

* Always start with `size="96pt,96pt" type="frame"` on the `<v:fill>`. This will center our image horizontally and vertically inside the VML shape.
* `origin="0.5,-0.5" position="0.5,-0.5"` equals `top right`
* `origin="-0.5,-0.5" position="-0.5,-0.5"` equals `top left`
* `origin="0.5,0.5" position="0.5,0.5"` equals `bottom right`
* `origin="-0.5,0.5" position="-0.5,0.5"` equals `bottom left`

We need to imagine the fill as an extra layer inside the VML shape. That layer takes the exact same size as the VML shape itself. The `position` attribute will move that entire layer inside the shape, with values from 0 to 1 being relative to the shape's size. The `origin` attribute will move the image inside that layer from its center, with values from 0 to 1 being relative to the image's size.

Full example:

```
<v:rect xmlns:v="urn:schemas-microsoft-com:vml" fill="true" stroke="false" style="width:450pt;height:225pt;">
	<v:fill origin="-0.5,0.5" position="-0.5,0.5" size="48pt,48pt" type="frame" src="https://i.imgur.com/SD8Lp27.png" />
</v:rect>
```