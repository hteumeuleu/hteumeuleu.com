---
title:  "Making Emails React to Outlook.com’s Dark Mode"
---

One thing I love about [Outlook.com’s dark mode](/2019/dealing-with-outlook-com-s-dark-mode/) is the button to toggle between dark mode and light mode for a message. A simple and handy option to quickly change the look of an email. (And I'm excited that a similar button is [coming to Firefox DevTools soon](https://twitter.com/FirefoxDevTools/status/1361361182511218700).)

<figure>
<a href="/uploads/2021/02/outlook-com-dark-mode-button.png"><img src="/uploads/2021/02/outlook-com-dark-mode-button.png" alt="" width="800" height="120" /></a>
<figcaption>Outlook.com’s dark mode button with the moon and sun icon.</figcaption>
</figure>

One thing I hate about this, though, is that if you carefully coded a dark mode for your email using a `@media (prefers-color-scheme:dark)` media query, that button might do nothing.

That’s because using `@media` always targets the browser at its highest level. So if you define mobile styles in a `@media (max-width:600px)` media query, but your email is displayed in a 500px wide preview pane in a webmail inside a 1280px wide browser window, none of those styles will apply.

And if your email is displayed in Outlook.com’s light mode preview pane, but your operating system is set up in dark mode, all of your dark mode styles will still apply. (The problem is very similar to [using `@supports` in an email](https://www.hteumeuleu.com/2017/the-trap-of-using-supports-in-emails/).)

Here’s an example of such a "problematic" dark mode media query:


```css
@media (prefers-color-scheme:dark) {
    .email-background {
        background-color: #423500 !important;
    }
}
```

<figure>
<a href="/uploads/2021/02/outlook-com-dark-mode-media-query.png"><img src="/uploads/2021/02/outlook-com-dark-mode-media-query.png" alt="" width="1128" height="765" /></a>
<figcaption>An example of email with dark modes applied in Outlook.com in both dark and light mode.</figcaption>
</figure>

In my latest clients work, I tried to find a solution to this.

## Excluding Outlook.com from Dark Mode Styles

The first step is to make sure that styles inside a `@media (prefers-color-scheme:dark)` won’t apply in Outlook.com. Outlook.com prefixes `class` and `id` attributes in HTML and CSS with an `x_`.

Using [an attribute selector in CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/Attribute_selectors), we can detect if a class was prefixed by Outlook.com (`[class^="x_"]`).

Combining this with [a `:not()` pseudo-class](https://developer.mozilla.org/en-US/docs/Web/CSS/:not) and we can now apply styles for an element whose `class` attribute doesn't start with `x_`.

```css
@media (prefers-color-scheme:dark) {
    .email-background:not([class^="x_"]) {
        background-color: #423500 !important;
    }
}
```

## Adding Outlook.com Dark Mode Specific Styles

The second step is to add back dark mode styles, but this time making them specific for Outlook.com only. This time, we’re going to take advantage of how Outlook.com alternates an email colors for its automatic dark mode.

When it sees an element that doesn’t have “_valid contrast_”, Outlook.com will fix it by changing either the element’s `color` and `background-color` in CSS or `color` and `bgcolor` attributes in HTML. What’s a “_valid contrast_”, you might ask? Well, [per Outlook.com’s source code](https://gist.github.com/hteumeuleu/51b5a8ea95cb47e344b0cb47bc1f2289#file-transformelementfordarkmode-ts-L102), it’s a color with at least a 4.5 contrast ratio compared to Outlook.com‘s dark background.

And when Outlook.com changes an element’s color, it will add a custom data attribute on said element to save the original color. There are four different possible attributes in total:

* `data-ogsc` (for “_original style color_”)
* `data-ogac` (for “_original attribute color_”)
* `data-ogsb` (for “_original style background_”)
* `data-ogac` (for “_original attribute background_”)

Using once again an attribute selector, we can target when Outlook.com has changed an element’s color and apply our own custom styles. But because [Outlook.com only supports attribute selector](https://www.caniemail.com/features/css-selector-attribute/) solo (`[attr]`) or with an element selector (`E[attr]`), we can not simply use a selector like `.email-background[data-ogsb]`. 

My prefered way to deal with this is to have a main element with a white background color (or any color that gets changed by Outlook.com). So then we can use the `data-ogsb` applied to that main element to target any child element. Here’s an example:

```css
[data-ogsb] .email-background {
    background-color: #423500 !important;
}
```

## Let There Be Light

And voilà! We now have an HTML email that reacts to Outlook.com’s own dark and light button.

<figure class="figure--large figure--grid">
    <img alt="" src="/uploads/2021/02/outlook-com-light-email-in-dark-mode.png" width="640" height="900">
    <img alt="" src="/uploads/2021/02/outlook-com-dark-email-in-dark-mode.png" width="640" height="900">
    <figcaption>A screenshot of an email in light mode (left) and dark mode (right), both while Outlook.com and the operating system are set up in dark mode.</figcaption>
</figure>

Here’s [the full code for the email above](https://codepen.io/hteumeuleu/pen/ZEBrJbp?editors=1000).

I’m aware that this technique might be a bit too hacky and not for every emails. My biggest fear is that this would ultimately remove support for Gmail (if Gmail were ever to support `@media (prefers-color-scheme:dark)` as rumored [almost a year ago](https://github.com/hteumeuleu/email-bugs/issues/68#issuecomment-629411633)).

But for now, I think I like it.