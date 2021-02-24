---
title:  "Making emails react to Outlook.com’s dark mode"
---

One thing I love about [Outlook.com’s dark mode](/2019/dealing-with-outlook-com-s-dark-mode/) is the button to toggle between dark mode and light mode for a message. A simple and handy option to quickly change the look of an email. (And I'm excited that a similar button is [coming to Firefox DevTools soon](https://twitter.com/FirefoxDevTools/status/1361361182511218700).)

<figure>
<a href="/uploads/2021/02/outlook-com-dark-mode-button.png"><img src="/uploads/2021/02/outlook-com-dark-mode-button.png" alt="" width="800" height="120" /></a>
<figcaption>Outlook.com’s dark mode button.</figcaption>
</figure>

One thing I hate about this, though, is that if you carefully coded a dark mode for your email using a `prefers-color-scheme` media query, that button might do nothing. Because you see, using `@media` always targets the browser at its highest level. So you might define mobile styles in a `@media (max-width:600px)` media query. But if your email is displayed in a 500px wide preview pane in a webmail inside a 1280px wide browser window, none of those styles will apply. And if your email is displayed in Outlook.com’s light mode preview pane but your operating system is set up in dark mode, all of your dark mode styles will still apply. (The problematic is very similar to [using `@supports` in an email](https://www.hteumeuleu.com/2017/the-trap-of-using-supports-in-emails/).)

In my latest clients work, I tried to find a solution to this.