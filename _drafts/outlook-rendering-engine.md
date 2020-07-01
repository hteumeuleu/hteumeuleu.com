---
title:  "Making sense of Outlook’s rendering engine"
---

In 2007, Apple introduced the first iPhone. The same year, Microsoft introduced Outlook 2007 on Windows and its brand new HTML and CSS rendering engine: **Word**. It makes a lot of sense from an authoring point of view. But let’s just say to keep things polite that Word is not very good at rendering HTML and CSS. And while Outlook on macOS, iOS or Android all use WebKit or Blink as a rendering engine, even the latest release on Windows (Outlook 2019) still uses Word. In 2007, the iPhone paved the way for a mobile future while Outlook doomed HTML emails to use deprecated code and weird quirks forever.

There are three things that will help us tame the beasts that are the Windows versions of Outlook (or *The Outlooks*, as I like to call them): Tables, tables, and more tables. But also conditional comments, `mso` properties, and VML. Let’s see each of those in more details.

# Tables

The only official existing documentation about Word’s rendering is a 2006’s post from Microsoft explaining [HTML and CSS Rendering Capabilities in Outlook 2007](https://docs.microsoft.com/en-us/previous-versions/office/developer/office-2007/aa338201(v=office.12)?redirectedfrom=MSDN). But it’s still enough to apprehend how Word’s engine works and why we’re going to need tables.

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

But more importantly, we must think about which element to use to apply certain styles. So if I have to define a `width` or an `height`, I will need a `<table>`. If I have to use a `padding`, I will also need a `<table>`. `border-left`? `<table>`!

To this day, *The Outlooks* on Windows are the sole reason we still use tables in HTML emails. Luckily, there are ways for us to only make those tables visible for Outlook, hiding them to more capable email clients and allowing us to use more semantic code.

# Conditional comments

# `mso` properties

# VML

---

In 2009, Microsoft justified the use of Word[1]:

> We’ve made the decision to continue to use Word for creating e-mail messages because we believe it’s the best e-mail authoring experience around, with rich tools that our Word customers have enjoyed for over 25 years.

From an authoring point of view, it makes a lot of sense. And because Word’s engine is so different, the only way to display HTML emails properly in Outlook is to use Word’s engine for rendering.


---
[1] https://web.archive.org/web/20110311083708/http://blogs.office.com/b/microsoft-outlook/archive/2009/06/24/the-power-of-word-in-outlook.aspx