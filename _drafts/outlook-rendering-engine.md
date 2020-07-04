---
title:  "Making sense of Outlook’s rendering engine"
---

In 2007, Apple introduced the first iPhone. The same year, Microsoft introduced Outlook 2007 on Windows and its brand new HTML and CSS rendering engine: **Word**. It makes a lot of sense from an authoring point of view. But let’s just say to keep things polite that Word is not very good at rendering HTML and CSS. And while Outlook on macOS, iOS or Android all use WebKit or Blink as a rendering engine, even the latest release on Windows (Outlook 2019) still uses Word. In 2007, the iPhone paved the way for a mobile future while Outlook doomed HTML emails to use deprecated code and weird quirks forever.

There are four things that will help us tame the beasts that are the Windows versions of Outlook (or *The Outlooks*, as I like to call them): **tables**, tables, tables and more tables. But also: **conditional comments**, **`mso` properties**, and **VML**.

Let’s see each of those in more details.

## Tables

The only official existing documentation about Word’s rendering is a 2006’s post from Microsoft explaining [HTML and CSS Rendering Capabilities in Outlook 2007](https://docs.microsoft.com/en-us/previous-versions/office/developer/office-2007/aa338201(v=office.12)?redirectedfrom=MSDN). But it’s still enough to comprehend how Word’s engine works and why we’re going to need tables.

Microsoft splits CSS properties into three categories:

1. **CORE**: `color`, `background-color`, font properties (`font`, `font-family`, `font-style`, `font-size`, `font-weight`, `text-decoration`, `text-align`, `vertical-align`, `letter-spacing`, `line-height`, `white-space`), border shorthand properties (`border`, `border-color`, `border-style`, `border-width`, `border-collapse`) and [a few others](https://docs.microsoft.com/en-us/previous-versions/office/developer/office-2007/aa338201(v=office.12)?redirectedfrom=MSDN#core).
2. **COREEXTENDED**: `text-indent` and margin properties (`margin`, `margin-left`, `margin-right`, `margin-top`, `margin-bottom`).
3. **FULL**: `width`, `height`, `padding` (as well as `padding-left`, `padding-right`, `padding-top`, `padding-bottom`) and border longhand properties (`border-left`, `border-left-color`, `border-left-width`, `border-left-style`, etc.).

And each of these categories will only apply to certain HTML elements:

* `<body>` and `<span>` only support **CORE** properties.
* `<div>` and `<p>` support both **CORE** and **COREEXTENDED** properties.
* All the other elements supported by Outlook (like `<table>`, `<td>`, `<h1>`, `<ul>`, `<li>`, …) support **CORE**, **COREEXTENDED** and **FULL** properties.

![CSS support in Outlook 2007-2019 diagram](/uploads/2020/07/css-support-in-outlook.png)

This means that not only we are limited by Outlook’s own capabilities. So no `border-radius` nor `background-image`, for example.

But more importantly, we must think about which element to use to apply certain styles. So if I have to define a `width` or an `height` on a generic container element, I will use a `<table>`. If I need a `padding`, I will also use a `<table>`. `border-left`? `<table>`!

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

We can also append a version number to target only a specific Outlook version. Note that the version number is the actual software version number (`12`, `14`, …), not the commercial name (2007, 2010, …).

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

Things got nice for a bit in 2016 because the version number (`16`) actually matched the commercial name (2016). But unfortunately for us, Microsoft decided to keep the exact same version number for Outlook 2019, so both are indistinguishable now. Oh, and because superstition is a thing, there’s never been a version 13.

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

Are conditional comments safe to use in HTML emails? Remember when I said *nothing works everywhere*? Well, the desktop webmail of the german provider T-Online.de renders content in conditional comments[2].


## `mso` properties

*The Outlooks*’ rendering engine also has a lot of proprietary properties prefixed by the `mso-` keyword. There are hundreds and hundreds of those, documented by Microsoft in [a <abbr title="Compiled HTML Help">CHM</abbr> file](https://docs.microsoft.com/en-us/previous-versions/office/developer/office2000/aa155477(v=office.10)?redirectedfrom=MSDN). (Jason Rodriguez has [a great story on how to get to that file](https://rodriguezcommaj.com/blog/dial-ed-for-mso), but fortunately Stig Morten Myre has [an online archive](https://stigmortenmyre.no/mso/html/concepts/ofconstyletable.htm).)

One interesting point is that for most standards CSS properties, Microsoft has an equivalent proprietary version prefixed by `mso-` and suffixed by `-alt`. So let’s say you want to define a `padding` value but only for *the Outlooks*, you can use the `mso-padding-alt` property.

```
<td style="mso-padding-alt:0 16px"></td>
```

Another thing is that some proprietary properties of Outlook can mimick their modern CSS equivalent. For example, `text-underline-color` in Outlook is the same as `text-decoration-color` in CSS. So if you want to apply a specific color to a text underline, you can use both properties:

```
text-underline-color: red; /* Outlook version */
text-decoration-color: red; /* Standard version for clients that supports it */
```

Another example is the property `mso-hide:all`, which is equivalent to `display:none` (to hide an element).

## VML

https://docs.microsoft.com/en-us/windows/win32/vml/web-workshop---specs---standards----how-to-use-vml-on-web-pages

---

In 2009, Microsoft justified the use of Word[1]:

> We’ve made the decision to continue to use Word for creating e-mail messages because we believe it’s the best e-mail authoring experience around, with rich tools that our Word customers have enjoyed for over 25 years.

From an authoring point of view, it makes a lot of sense. And because Word’s engine is so different, the only way to display HTML emails properly in Outlook is to use Word’s engine for rendering.


---
[1] https://web.archive.org/web/20110311083708/http://blogs.office.com/b/microsoft-outlook/archive/2009/06/24/the-power-of-word-in-outlook.aspx
[2] https://github.com/hteumeuleu/email-bugs/issues/43
