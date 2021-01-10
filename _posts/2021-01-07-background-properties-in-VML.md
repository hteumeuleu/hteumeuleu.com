---
title:  "Background properties in VML"
---

I recently got to do some research around VML background images. And here’s what I learnt about simulating background CSS properties like `background-repeat`, `background-size` and `background-position` in VML.

But first, a bit of background (_ha_) about the basics of VML and background images.

## Basics

VML is supported in _The Outlooks_ on Windows using Word’s rendering engine (from 2007 to 2019). In order to use it, you need to declare the VML namespace in your code (`xmlns:v="urn:schemas-microsoft-com:vml"`). You can do this inline for every VML element you will use.

```xml
<v:rect xmlns:v="urn:schemas-microsoft-com:vml">
</v:rect>
```

_Or_ you can declare it once and for all as an attribute of the `<html>` element of your email. (This is usually my preferred way to do it.)

```xml
<html lang="en" xmlns:v="urn:schemas-microsoft-com:vml">
```

You can then use any VML shape like  [`<v:rect>`](https://docs.microsoft.com/en-us/windows/win32/vml/msdn-online-vml-rect-element) or [`<v:oval>`](https://docs.microsoft.com/en-us/windows/win32/vml/msdn-online-vml-oval-element). We will use the `stroked="false"` attribute to remove the default border around VML shapes and a `style` attribute to define the `width` and `height` of the shape. (One VML oddity is that attributes like `stroked` or `filled` can also be written `stroke` or `fill`. I tend to prefer the former because it matches Microsoft documentation, even though the latter matches [the VML specification](https://www.w3.org/TR/NOTE-VML).)

```xml
<v:rect stroked="false" style="width:600px;height:300px;">
</v:rect>
```

Then, inside that shape, we’ll use the [`<v:fill>`](https://docs.microsoft.com/en-us/windows/win32/vml/msdn-online-vml-fill-element) element to define our background fill with various attributes detailed below.

```xml
<v:rect stroked="false" style="width:600px;height:300px;">
  <v:fill type="frame" color="#add5f7" src="https://i.imgur.com/pmS2PDm.png" />
</v:rect>
```

We can then include our HTML foreground content inside a [`<v:textbox>`](https://docs.microsoft.com/en-us/windows/win32/vml/msdn-online-vml-textbox-element) element.

```xml
<v:rect stroked="false" style="width:600px;height:300px;">
  <v:fill type="frame" color="#add5f7" src="https://i.imgur.com/pmS2PDm.png" />
  <v:textbox inset="0,0,0,0">
  	 <h1>Hello World!</h1>
  </v:textbox>
</v:rect>
```

If we want the background to adjust to the content, we can omit the `height` declaration on the `<v:rect>` element and apply a `style="mso-fit-shape-to-text:true;"` to the `<v:textbox>` element.

Because VML is only supported in _The Outlooks_ (and versions of Internet Explorer 9 and below), I always wrap VML code between [conditional comments](https://docs.microsoft.com/en-us/previous-versions/windows/internet-explorer/ie-developer/compatibility/ms537512(v%3dvs.85)) for Outlook using the `[if mso]` expression. It’s also worth mentioning that there is an `[if vml]` expression, which would target all rendering engines supporting VML (like _The Outlooks_ and IE9 and below).

```xml
<!--[if mso]>
<v:rect stroked="false" style="width:600px;height:300px;">
  <v:fill type="frame" color="#add5f7" src="https://i.imgur.com/pmS2PDm.png" />
</v:rect>
<![endif]-->
```

For the sake of syntax highlighting, I will not put these conditional comments for further examples below. But make sure to always have them around your VML code.

## background-color

In VML, [`background-color`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-color) can be replicated via the [`color` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/color-attribute--fill--vml) of the `<v:fill>` element. (This overrides the `FillColor` attribute you can apply directly on any VML shape.)

```xml
<v:rect stroked="false" style="width:600px;height:300px;">
  <v:fill color="#add5f7" />
</v:rect>
```

## background-image

In VML, [`background-image`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-image) can be replicated via the [`src` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/src-attribute--fill--vml) of the `<v:fill>` element.

```xml
<v:rect stroked="false" style="width:600px;height:300px;">
  <v:fill src="https://i.imgur.com/pmS2PDm.png" color="#add5f7" />
</v:rect>
```

## background-repeat

In VML, [`background-repeat`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-repeat) can be replicated via the [`type` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/type-attribute--fill--vml) of the `<v:fill>` element. There are two possible values that can mimick a CSS equivalent:

* `type="frame"` (equivalent to `background-repeat:no-repeat`)
* `type="tile"` (equivalent to `background-repeat:repeat`)

As far as I know, it is not possible to simply define an equivalent to the CSS values `repeat-x` or `repeat-y` (nor `space` or `round`, but those are very rarely used in CSS anyway).

```xml
<v:rect stroked="false" style="width:600px;height:300px;">
  <v:fill type="frame" src="https://i.imgur.com/pmS2PDm.png" color="#add5f7" />
</v:rect>
```

Using a `type="frame"` value will make the image fit the entire shape by default. Fortunately this is something we can manage with the following section.


## background-size

In VML, keyword values `contain` and `cover` of [`background-size`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-size) can be replicated via the [`aspect` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/msdn-online-vml-aspect-attribute) of the `<v:fill>` element.

* `aspect="atleast"` (equivalent to `background-size:cover`)
* `aspect="atmost"` (equivalent to `background-size:contain`)

We can also define exact dimensions via the [`sizes` attribute](https://docs.microsoft.com/en-us/windows/win32/vml/size-attribute--fill--vml) of the `<v:fill>` element. Both dimensions (width and height of the image) need to be set, separated by either a space or a comma (so `sizes="64px 64px"` and `sizes="64px,64px"` are the same).

Sizes defined in pixels won’t be adjusted when Outlook is rendering at 120 dpi. So I recommend to always use values in points (where 1 pixel equals 0.75 point). The previous example would become: `sizes="48pt,48pt"`.

An unexpected effect of `sizes` in VML is that it will also apply to the background color. So in our example, the background color will only be visible across our 48×48pt square.

```xml
<v:rect stroked="false" style="width:600px;height:300px;">
  <v:fill sizes="48pt,48pt" type="frame" src="https://i.imgur.com/pmS2PDm.png" color="#add5f7" />
</v:rect>
```

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

In my experience, positioning a background image in VML is slightly different whether the image is repeated or not.

### Non repeated background image

With a non repeated background image (`type="frame"` in VML), we’ll play with both the `origin` and `position` attributes. Here are different equivalent to usual values in CSS.

* `origin="-0.5,-0.5" position="-0.5,-0.5"` equals `top left`
* `origin="0.5,-0.5" position="0.5,-0.5"` equals `top right`
* `origin="-0.5,0.5" position="-0.5,0.5"` equals `bottom left`
* `origin="0.5,0.5" position="0.5,0.5"` equals `bottom right`

```xml
<v:rect stroked="false" style="width:600px;height:300px;">
  <v:fill origin="0.5,0.5" position="0.5,0.5" type="frame" sizes="48pt,48pt" src="https://i.imgur.com/pmS2PDm.png" color="#add5f7" />
</v:rect>
```

### Repeated background image

With a repeated background image (`type="tile"` in VML), we’ll only need the `position` attribute. Here are different equivalent to usual values in CSS.

* `position="0,0"` equals `top left`
* `position="1,0"` equals `top right`
* `position="0,1"` equals `bottom left`
* `position="1,1"` equals `bottom right`

```xml
<v:rect stroked="false" style="width:600px;height:300px;">
  <v:fill position="1,1" type="tile" sizes="48pt,48pt" src="https://i.imgur.com/pmS2PDm.png" color="#add5f7" />
</v:rect>
```

## Conclusion

You can find an overall [code example here](https://lab.hteumeuleu.com/backgrounds/) (and here is [the corresponding Email on Acid test](https://app.emailonacid.com/app/acidtest/f2581pXVHqHOgWh0TjQqpJBF2bzjhOUb4ntBnHPaz4nO2/list)).

Last year, in [my Outlook article](/2020/outlook-rendering-engine/), I stated that: 

> VML is still very much unexplored territory in the email world and I strongly believe that there’s a lot more to discover.

I can’t emphasize this enough as I learn more and more about VML. I invite you to read through Mark Robbins’ own experiments (like [CSS triangles](https://www.goodemailcode.com/email-enhancements/css-triangles) or [SVG to VML](https://www.goodemailcode.com/email-enhancements/svg-to-vml)).

Let me know if you learn anything exciting about VML!
