---
title:  "Background properties in VML"
---

I recently got to do a little bit of research around VML background images. And here’s what I learnt about simulating background CSS properties like `background-repeat`, `background-size` and `background-position` in VML.

## background-repeat

In VML, [`background-repeat`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-repeat) can be replicated via the [`type` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/type-attribute--fill--vml) of the `<v:fill>` element. There are two possible values that can mimick a CSS equivalent:

* `type="frame"` (equivalent to `background-repeat:no-repeat`)
* `type="tile"` (equivalent to `background-repeat:repeat`)

As far as I know, it is not possible to simply define an equivalent to the CSS values `repeat-x` or `repeat-y` (nor `space` or `round`, but those are very rarely used in CSS anyway).

## background-size

In VML, keyword values `contain` and `cover` of [`background-size`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-size) can be replicated via the [`aspect` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/msdn-online-vml-aspect-attribute) of the `<v:fill>` element.

* `aspect="atleast"` (equivalent to `background-size:cover`)
* `aspect="atmost"` (equivalent to `background-size:contain`)

We can also define exact dimensions via the [`sizes` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/size-attribute--fill--vml) of the `<v:fill>` element. Both dimensions (width and height of the image) need to be set, separated by either a space or a comma (so `sizes="64px 64px"` and `sizes="64px,64px"` are the same).

Sizes defined in pixels won’t be adjusted when Outlook is rendering at 120 dpi. So I recommend to always use values in points (where 1 pixel equals 0.75 point). The previous example would become: `sizes="48pt,48pt"`.

## background-position

In VML, [`background-position`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-position) can be replicated using the [`origin` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/origin-attribute--fill--vml) and the [`position` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/position-attribute--fill--vml). Both attributes require two coordinates (x and y), separated by a space or a comma. Each coordinate is a fraction value (between 0 and 1) relative to the width and height of the image for the `position` attribute, and relative to the width and height of the VML shape for the `origin` attribute. The coordinates move from the top left of the VML shape for the `origin` attribute, while they move from the center of the image for the `position` attribute.

<figure>
<a href="/uploads/2021/01/vml-origin-position-coordinates.png"><img src="/uploads/2021/01/vml-origin-position-coordinates.png" alt="" width="800" height="630" /></a>
</figure>

For example, for a `64x64` image inside a `600x300` VML shape, setting the `position` attribute to `1,1` would


With a non repeated background image (`type="frame"` in VML), you can define the image background position .

* `origin="-0.5,-0.5" position="-0.5,-0.5"` equals `top left`
* `origin="0.5,-0.5" position="0.5,-0.5"` equals `top right`
* `origin="-0.5,0.5" position="-0.5,0.5"` equals `bottom left`
* `origin="0.5,0.5" position="0.5,0.5"` equals `bottom right`

We need to imagine the fill as an extra layer inside the VML shape. That layer takes the exact same size as the VML shape itself. The `position` attribute will move that entire layer inside the shape, with values from 0 to 1 being relative to the shape's size. The `origin` attribute will move the image inside that layer from its center, with values from 0 to 1 being relative to the image's size.

Full example:

```xml
<v:rect xmlns:v="urn:schemas-microsoft-com:vml" fill="true" stroke="false" style="width:450pt;height:225pt;">
	<v:fill origin="-0.5,0.5" position="-0.5,0.5" size="48pt,48pt" type="frame" src="https://i.imgur.com/SD8Lp27.png" />
</v:rect>
```