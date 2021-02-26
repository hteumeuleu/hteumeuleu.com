---
title:  "Making emails react to Outlook.com’s dark mode"
---

One thing I love about [Outlook.com’s dark mode](/2019/dealing-with-outlook-com-s-dark-mode/) is the button to toggle between dark mode and light mode for a message. A simple and handy option to quickly change the look of an email. (And I'm excited that a similar button is [coming to Firefox DevTools soon](https://twitter.com/FirefoxDevTools/status/1361361182511218700).)

<figure>
<a href="/uploads/2021/02/outlook-com-dark-mode-button.png"><img src="/uploads/2021/02/outlook-com-dark-mode-button.png" alt="" width="800" height="120" /></a>
<figcaption>Outlook.com’s dark mode button.</figcaption>
</figure>

One thing I hate about this, though, is that if you carefully coded a dark mode for your email using a `@media (prefers-color-scheme:dark)` media query, that button might do nothing. Because you see, using `@media` always targets the browser at its highest level. So you might define mobile styles in a `@media (max-width:600px)` media query. But if your email is displayed in a 500px wide preview pane in a webmail inside a 1280px wide browser window, none of those styles will apply. And if your email is displayed in Outlook.com’s light mode preview pane but your operating system is set up in dark mode, all of your dark mode styles will still apply. (The problematic is very similar to [using `@supports` in an email](https://www.hteumeuleu.com/2017/the-trap-of-using-supports-in-emails/).)

```css
@media (prefers-color-scheme:dark) {
    .email-background {
        background-color: #423500 !important;
    }
}
```

<figure>
<a href="/uploads/2021/02/outlook-com-dark-mode-media-query.png"><img src="/uploads/2021/02/outlook-com-dark-mode-media-query.png" alt="" width="1128" height="765" /></a>
<figcaption>An email with dark mode styles in a media query will stay the same in a dark mode OS.</figcaption>
</figure>

In my latest clients work, I tried to find a solution to this.

# How Outlook.com processes dark mode

* Prefixing with `_x`
* DarkModeHandler: https://gist.github.com/hteumeuleu/51b5a8ea95cb47e344b0cb47bc1f2289

# Solution

```css
@media (prefers-color-scheme:dark) {
    .email:not([class^="x_"]) {
        background-color: #2f4554 !important;
    }

    .email-edito:not([class^="x_"]) {
        background-color: #112f42 !important;
    }
}
```

```css
[data-ogsb] .email {
    background-color: #2f4554 !important;
}

[data-ogsb] .email-edito {
    background-color: #112f42 !important;
}
```