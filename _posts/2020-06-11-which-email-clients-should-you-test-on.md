---
title:  "Which email clients should you test on?"
---

Testing is a very important part of coding robust HTML emails. But you might not always have the time to do extensive tests. Here's the top three email clients I recommend testing in.

1. **Gmail Apps (iOS or Android) with a Non Gmail Account** (also kown as GANGA or Gmail IMAP). This gets you through most of Gmail quirks (its subset of supported HTML and CSS, its freakiness to remove entire `style` attributes if there's something it doesn't like in it, …) plus the extra layer of GANGA, meaning no `<style>` and no media queries support.
2. **Outlook 2016 on Windows at 120dpi**. This gets you through Outlook's rendering engine, plus the extra weirdness of [rendering at 120 dpi](https://www.courtneyfantinato.com/correcting-outlook-dpi-scaling-issues/). And why 2016 and not 2019? Because 2019 actually has extra support for a few things like `max-width`. So 2016 is more reflective of Outlook versions on Windows, all the way back to Outlook 2007.
3. **Outlook.com on desktop with dark mode enabled.** This gets you through the usual filters webmails have, a very restricted display's width for emails, plus the dark mode color changer.

If you can make your emails look good on these clients, then you've probably tackled about 80% of the most common email quirks and bugs.

Then what? I'd probably go with **Apple Mail on the latest version of iOS**, and **Gmail's desktop webmail**. Not because they're especially quirky, but because they're widely popular.

Then you can also throw a test on **Yahoo's Android app**. This will cover almost all quirks from AOL and Yahoo's client (plus [that extra double `<head>` hack required for Yahoo Android](https://github.com/hteumeuleu/email-bugs/issues/28)).

After all that, you can test in more esoteric clients (Samsung Email, Mozilla Thunderbird, …) or regional ones (Mail.ru, Orange.fr, …). And then you can keep going on forever with any email client you can find.

There's [a quote by Ethan Marcotte](https://ethanmarcotte.com/wrote/left-to-our-own-devices/) that I love:

> Your website’s only as strong as the weakest device you’ve tested it on.

This applies extremely well to emails. **Your email's only as strong as the weakest email client you've tested it on.**

Start to make your email look good in the worst email client your want to support. And then ensure it's still good, or even make it better, if more capable email clients. If you take a look at [Can I email's scoreboard](https://www.caniemail.com/scoreboard/), you can take this as starting to test clients at the bottom of the ranking. And then make make your way to the top.
