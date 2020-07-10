---
title:  "Making sense of Outlook’s rendering engine"
favorite: true
---

In 2007, Apple introduced the first iPhone. The same year, Microsoft introduced Outlook 2007 on Windows and its brand new HTML and CSS rendering engine: **Word**. It makes a lot of sense from an authoring point of view. But let’s just say to keep things polite that Word is not very good at rendering HTML and CSS. And while Outlook on macOS, iOS or Android all use WebKit or Blink as a rendering engine, even the latest release on Windows (Outlook 2019) still uses Word. In 2007, the iPhone paved the way for a mobile future while Outlook doomed HTML emails to use deprecated code and weird quirks forever.

There are four things that will help us tame the beasts that are the Windows versions of Outlook (or *The Outlooks*, as I like to call them): **tables**, tables, tables and more tables. But also: **conditional comments**, **`mso` properties**, and **VML**.

Let’s see each of those in more details.

## Tables

The only official existing documentation about Word’s rendering is a 2006’s post from Microsoft explaining [HTML and CSS Rendering Capabilities in Outlook 2007](https://docs.microsoft.com/en-us/previous-versions/office/developer/office-2007/aa338201(v=office.12)?redirectedfrom=MSDN). But it’s still enough to comprehend how Word’s engine works and why we’re going to need tables.

Microsoft splits CSS properties into three categories:

1. **CORE**: `color`, `background-color`, text properties (`font`, `font-family`, `font-style`, `font-size`, `font-weight`, `text-decoration`, `text-align`, `vertical-align`, `letter-spacing`, `line-height`, `white-space`), border shorthand properties (`border`, `border-color`, `border-style`, `border-width`, `border-collapse`) and [a few others](https://docs.microsoft.com/en-us/previous-versions/office/developer/office-2007/aa338201(v=office.12)?redirectedfrom=MSDN#core).
2. **COREEXTENDED**: `text-indent` and margin properties (`margin`, `margin-left`, `margin-right`, `margin-top`, `margin-bottom`).
3. **FULL**: `width`, `height`, `padding` (as well as `padding-left`, `padding-right`, `padding-top`, `padding-bottom`) and border longhand properties (`border-left`, `border-left-color`, `border-left-width`, `border-left-style`, etc.).

And each of these categories will only apply to certain HTML elements:

* `<body>` and `<span>` only support **CORE** properties.
* `<div>` and `<p>` support both **CORE** and **COREEXTENDED** properties.
* All the other elements supported by Outlook (like `<table>`, `<td>`, `<h1>`, `<ul>`, `<li>`, …) support **CORE**, **COREEXTENDED** and **FULL** properties.

<figure class="figure--large">
<img src="/uploads/2020/07/css-support-in-outlook.png" alt="CSS support in Outlook 2007-2019 diagram" />
<figcaption>Diagram showing CSS support in Outlook 2007-2019 on Windows</figcaption>
</figure>

This means that not only we are limited by Outlook’s own capabilities. (No `background-image` nor `border-radius`, for example.)

But more importantly, we must think about which element to use to apply certain styles. So if I have to define a `width` or an `height` on a generic container element, I will use a `<table>`. If I need a `padding`, I will also use a `<table>`. `border-left`? `<table>`!

And even then, *The Outlooks* support of CSS is sometimes quirky and different from Web standards. Here are three examples:

* While `margin` is somewhat supported, `auto` values are not supported. So the `margin:0 auto` we’d use on the web to center a table won’t work. We’ll use the `align="center"` attribute instead.
* In a table row (`<tr>`), the vertical padding on cells (`<td>`) will be the biggest value of that row. So if we have a cell with `padding-top:10px` and another one with `padding-top:20px`, both cells will have `20px`.
* `background-color` is well supported, but contrary to web standards, colors “bleed” and apply through margins. It’s usually safer and more consistent to apply a `background-color` to a table.

To this day, *The Outlooks* on Windows are the sole reason we still use tables in HTML emails. Luckily, there are ways for us to only make those tables visible for Outlook, hiding them to more capable email clients and allowing us to use more semantic code.

## Conditional comments

Microsoft introduced [conditional comments](https://docs.microsoft.com/en-us/previous-versions/windows/internet-explorer/ie-developer/compatibility/ms537512(v%3dvs.85)) back in 1999 in Internet Explorer 5. The idea is clever: inside a regular HTML comment (`<!-- -->`), you can code a condition that will make the rest of the content visible if fulfilled. Here’s an example:

```
<!--[if IE]>
<p>This is only visible in Internet Explorer.</p>
<![endif]-->
```

Conditional comments were removed starting Internet Explorer 10. But they’re still supported in all Windows versions of Outlook using Word’s rendering engine. Instead of using `IE` as a condition, we’re going to use the `mso` keyword.

```
<!--[if mso]>
<p>This is only visible in Outlook 2007-2019 on Windows.</p>
<![endif]-->
```

We can also append a version number to target only a specific Outlook version. Note that the version number is the actual software version number (`12`, `14`, …), not the commercial name (2007, 2010, …).

```
<!--[if mso 12]>
<p>This is Outlook 2007.</p>
<![endif]-->

<!--[if mso 14]>
<p>This is Outlook 2010.</p>
<![endif]-->  
<!--[if mso 15]>
<p>This is Outlook 2013.</p>
<![endif]-->  
<!--[if mso 16]>
<p>This is Outlook 2016. And 2019.</p>
<![endif]-->
```

Things got nice for a bit in 2016 because the version number (`16`) actually matched the commercial name (2016). But unfortunately for us, Microsoft decided to keep that same version number for Outlook 2019, so both are indistinguishable now. Oh, and because superstition is a thing, there’s never been a version 13.

We can also use operators to create more complex conditions, like `gte` (*greater-than or equal*) or `lte` (*less-than or equal*).

```
<!--[if gte mso 9]>
<p>This is Outlook 2007 and above.</p>
<![endif]-->
```

Even though this example is extremely popular in code you can find online, I recommend to use the `[if mso]` condition to target all Outlook versions using Word’s rendering engine.

Another operator I use is the *NOT* operator (`!`) that lets us build content for everything but *The Outlooks*.

```
<!--[if !mso]><!-->
<p>This is everything but The Outlooks.</p>
<!--<![endif]-->
```

Are conditional comments safe to use in HTML emails?

Well, the desktop webmail of the german provider [T-Online.de renders content in conditional comments](https://github.com/hteumeuleu/email-bugs/issues/43). So if that’s a concern for you, you can hack your way using an `[if false]` conditional comment (that T-Online will still parse and show).

## `mso` properties

*The Outlooks*’ rendering engine also has a lot of proprietary properties, mostly recognizable thanks to the `mso-` prefix. There are hundreds and hundreds of those, documented by Microsoft in [a <abbr title="Compiled HTML Help">CHM</abbr> file](https://docs.microsoft.com/en-us/previous-versions/office/developer/office2000/aa155477(v=office.10)?redirectedfrom=MSDN). (Jason Rodriguez has [a great story on how to get to that file](https://rodriguezcommaj.com/blog/dial-ed-for-mso), but fortunately Stig Morten Myre has [an online archive](https://stigmortenmyre.no/mso/html/concepts/ofconstyletable.htm).)

One interesting point is that for most standards CSS properties, Microsoft has an equivalent proprietary version prefixed by `mso-` and suffixed by `-alt`. So let’s say you want to define a `padding` value but only for *the Outlooks*, you can use the `mso-padding-alt` property.

```
<td style="mso-padding-alt:0 16px"></td>
```

Another thing is that some proprietary properties of Outlook can mimick their modern CSS equivalent. For example, `text-underline-color` in Outlook is the same as `text-decoration-color` in CSS. So if you want to apply a specific color to a text underline, you can use both properties:

```
text-underline-color: red; /* Outlook version */
text-decoration-color: red; /* Standard version for clients that supports it */
```

Another example is the property `mso-hide:all`, which is equivalent to `display:none` (to hide an element). (One difference worth noting is that `mso-hide:all` won’t cascade to children tables. So if you want to hide a table and all the tables nested in it, you will need to repeat `mso-hide:all` on every table.)

Stig Morten Myre has a great article explaining [how to fix bugs with Outlook specific CSS](https://cm.engineering/fixing-bugs-with-outlook-specific-css-f4b8ae5be4f4), including tips on how to use `mso-text-raise` or `mso-line-height-rule: exactly`.

## VML

<abbr title="Vector Markup Language">VML</abbr> is Microsoft’s take on a vector graphics format from the late nineties, right before SVG came around. Just like SVG, you can draw content with markup code. For example, if we want to draw a red rectangle, we can use the `<v:rect>` element and the following code:

```
<v:rect xmlns:v="urn:schemas-microsoft-com:vml" fillcolor="red" stroked="false" style="width:200px; height:100px;"></v:rect>
```

In order to use VML, we need to add the namespace declaration (`xmlns:v="urn:schemas-microsoft-com:vml"`). It can either be repeated inline for each VML element we’ll use. Or it can be added on the `<html>` element only once. And because VML will only work in *The Outlooks*, we’ll make sure to wrap it in a conditional comment. Here’s a full working example for our previous red rectangle.

```
<!DOCTYPE html>
<html lang="en" xmlns:v="urn:schemas-microsoft-com:vml">
<head>
    <title>VML rectangle</title>
</head>
<body>
    <!--[if mso]>
    <v:rect fillcolor="red" stroked="false" style="width:200px; height:100px;"></v:rect>
    <![endif]-->
</body>
</html>
```

One thing that got me about VML at first is that the syntax is very permissive. Boolean attributes can either be `true` or `false`, `t` or `f`, or `0` or `1`. And VML can also be written lowercase, uppercase, camelcase, etc.

Of course we can do more exciting things than red rectangles. One way we can use VML is to fake properties unsupported by *The Outlooks*. For example: background images. If we want to show a background image on our `<body>`, we can use the `<v:background>` element. Here’s an example:

```
<!DOCTYPE html>
<html lang="en" xmlns:v="urn:schemas-microsoft-com:vml">
<head>
    <title>VML background</title>
</head>
<body style="background:#e2ecb7 url(https://i.imgur.com/ftd0S2j.jpg);">
    <!--[if mso]>
    <v:background fill="true">
        <v:fill type="tile" color="#e2ecb7" src="https://i.imgur.com/ftd0S2j.jpg"></v:fill>
    </v:background>
    <![endif]-->
</body>
</html>
```

Campaign Monitor’s [Backgrounds.cm](https://www.backgrounds.cm) and [Buttons.cm](https://www.buttons.cm) make extensive use of VML to fake background images or rounded corners.

Mark Robbins has great examples on how VML can be used to [create CSS triangles](https://www.goodemailcode.com/email-enhancements/css-triangles) or [fake absolute positioning](https://www.goodemailcode.com/email-enhancements/faux-absolute-position).

VML is still very much unexplored territory in the email world and I strongly believe that there’s a lot more to discover. Back in the Internet Explorer 6 era, VML was vastly used to fake new CSS properties via JavaScript librairies like [CSS3 PIE](http://css3pie.com/) or [cufón](http://cufon.shoqolate.com/generate/) and I’m sure there’s a lot of inspiration to take here.

Microsoft’s [How to use VML on Webpages](https://docs.microsoft.com/en-us/windows/win32/vml/web-workshop---specs---standards----how-to-use-vml-on-web-pages) and [VML Object Model Reference](https://docs.microsoft.com/en-us/windows/win32/vml/msdn-online-vector-markup-language-object-model-reference) are great places to get started to learn about VML.

## Will Outlook on Windows ever be good?

The more I spend time with *The Outlooks*, the more I like to say that they’re not all bad. They’re just different. Using tables, conditional comments, proprietary properties and VML can be enough to work around most Outlook quirks. And even provide things that couldn’t be done elsewhere.

After the launch of Outlook 2007, the now defunct [Email Standards Project](https://www.email-standards.org/) launched a campaign to [Fix Outlook](https://web.archive.org/web/20110309041616/http://fixoutlook.org/) which resulted in [the Outlook team hanging a poster on their wall](https://www.email-standards.org/blog/entry/microsoft-prove-theyre-listening/index.html). And that was pretty much it.

In 2016, Litmus launched [a partnership with Microsoft](https://litmus.com/microsoft-partnership). It brought support for `max-width` in Outlook 2019 and a different font fallback when using web fonts. And that was pretty much it.

[Microsoft justified the use of Word in Outlook](https://web.archive.org/web/20110311083708/http://blogs.office.com/b/microsoft-outlook/archive/2009/06/24/the-power-of-word-in-outlook.aspx) in 2009:

> We’ve made the decision to continue to use Word for creating e-mail messages because we believe it’s the best e-mail authoring experience around, with rich tools that our Word customers have enjoyed for over 25 years.

My take is that **Outlook on Windows will die with Word’s rendering engine**. As the world is moving away from desktop computers, Microsoft is pushing its webmail and its mobile apps (which are all very good in comparison). And even if Microsoft moved away from Word in the next iteration of Outlook on Windows, it will probably take a decade before all the previous versions are upgraded. (Microsoft is just ending support from Outlook 2010 this year, in 2020.) So *The Outlooks* and Word’s rendering engine will still be around in 2030, but hopefully with a very low market share.