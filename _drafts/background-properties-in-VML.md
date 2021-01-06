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

In VML, [`background-position`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-position) can be replicated using the [`origin` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/origin-attribute--fill--vml) and the [`position` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/position-attribute--fill--vml). I found [Microsoft’s documentation](https://docs.microsoft.com/en-us/windows/win32/vml/msdn-online-vml-fill-element) and [the official VML specification](https://www.w3.org/TR/NOTE-VML) super confusing about those attributes, sometimes not reflecting my experience with them at all. So here’s my own practical understanding on how these work in _the Outlooks_.

Both attributes require two coordinates (x and y), separated by a space or a comma. Each coordinate is a fraction value relative to the width and height (between 0 and 1) of the image for the `origin` attribute, and of the VML shape for the `position` attribute. The coordinates move from the top left of the VML shape for the `position` attribute, while they move from the center of the image for the `origin` attribute.

I like to picture myself the fill as an extra layer inside the VML shape. That layer takes the exact same size as the VML shape itself. The `position` attribute will move that entire layer inside the shape, with values being relative to the shape's size (from 0 to 1). The `origin` attribute will move the image inside that layer from its center, with values being relative to the image's size (from 0 to 1).

<figure>
<a href="/uploads/2021/01/vml-origin-position-coordinates.png"><img src="/uploads/2021/01/vml-origin-position-coordinates.png" alt="" width="800" height="630" /></a>
<figcaption>Visual representation of my understanding of coordinates for the <code>position</code> and <code>origin</code> attributes with a <code>type="frame"</code> fill in VML.</figcaption>
</figure>

For example, if we wanted to move a `64x64` image to the bottom right of a `600x300` VML shape, we’d first define the `position` to `0.5, 0.5`. This would move the entire fill layer half the size of the shape horizontally (`300px`) and vertically (`150px`). This would make the actual image partially visible in the bottom right corner. In order to make it reappear completely, we’d then define the `origin` attribute to `0.5, 0.5`. This would move the background image itself half its size horizontally (`32px`) and vertically (`32px`).

<figure>
<a href="/uploads/2021/01/vml-background-image-bottom-right.png"><img src="/uploads/2021/01/vml-background-image-bottom-right.png" alt="" width="800" height="464" /></a>
<figcaption>The <code>position</code> attribute moves the fill layer (in blue) while the <code>origin</code> attribute moves the background image itself (in red).</figcaption>
</figure>

### Non repeated background image

With a non repeated background image (`type="frame"` in VML), here are different equivalent to usual values in CSS.

* `origin="-0.5,-0.5" position="-0.5,-0.5"` equals `top left`
* `origin="0.5,-0.5" position="0.5,-0.5"` equals `top right`
* `origin="-0.5,0.5" position="-0.5,0.5"` equals `bottom left`
* `origin="0.5,0.5" position="0.5,0.5"` equals `bottom right`


### Repeated background image

TK 

Full example:

```xml
<v:rect xmlns:v="urn:schemas-microsoft-com:vml" fill="true" stroke="false" style="width:450pt;height:225pt;">
	<v:fill origin="-0.5,0.5" position="-0.5,0.5" size="48pt,48pt" type="frame" src="https://i.imgur.com/SD8Lp27.png" />
</v:rect>
```