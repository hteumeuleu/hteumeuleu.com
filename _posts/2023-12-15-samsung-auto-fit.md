---
title: "Samsung Auto-fit"
---

This week, I got bit pretty hard by a bug I somehow managed to completely avoid until now. This bug involves three ingredients: the [Samsung Email](https://play.google.com/store/apps/details?id=com.samsung.android.email.provider) app on Android, its <em>Auto-fit</em> feature, and an Outlook account. Here’s everything I learnt about this.

It all started on a friday afternoon as I received an email from a client I’ve been working with in the past few months. Apparently, the fluid/hybrid templates I’ve carefully hand coded were rendering as the desktop version in the Samsung Email app on Android. And of course, the main boss of the organization used a Samsung phone with the <em>Samsung Email</em> app. So I had to fix this.

My first problem was… The <em>Samsung Email</em> app can only be installed on a Samsung device. And I didn’t have any Samsung device at hand. And the Samsung app isn’t part of Email of Acid or Litmus either. So I did what any email developer should do in this situation: ask for help in [the #emailgeeks Slack](https://email.geeks.chat/). 

<figure class="figure">
<picture>
    <source srcset="/uploads/2023/12/slack-dark.png" media="(prefers-color-scheme:dark)" />
    <img loading="lazy" src="/uploads/2023/12/slack-light.png" alt="A screenshot of my cry for help in Slack." width="640" height="130" />
</picture>
<figcaption><q>A client is telling me she's seeing the desktop version of an email in her Samsung Galaxy 8's default samsung email app. Are there any known issues with this email client? Would anyone with the Samsung email app be available to help me troubleshoot this? I don't have any Samsung phone at hands right now.</q></figcaption>
</figure>

Thankfully, [Mark Robbins](https://mastodon.social/@M_J_Robbins) came to the rescue and offered his help. He quickly pointed me at [this issue](https://github.com/hteumeuleu/email-bugs/issues/73) he reported on my [email-bugs repo on GitHub](https://github.com/hteumeuleu/email-bugs/) back in 2019. I glanced at the thread and the problem seemed related to a feature of the <em>Samsung Email</em> app called <em>Auto-fit</em>. The feature is supposed to <q>shrink email content to fit the screen</q>. But for some emails already optimised for mobile, it turns out it would actually do the exact opposite. Mark confirmed this was the problem and that my email worked fine with <em>Auto-fit</em> disabled. But since the feature is enabled by default, I needed to find a fix.

Luckily for me, @gabo recently posted [a solution on the Mosaico blog](https://mosaico.io/email-client-tricks/samsung-email-autowidth-breaks-repositivity/). The fix only requires to include the following style in your email.

```css
@media screen and (max-width: 384px) {
    .mail-message-content {
        width: 414px !important;
    }
}
```

Mark was able to test with this fix and confirmed it did the trick. Hurray! Job done! Congratulations! I sent the fixed version to my client and called it a day.

Except that…

A few moments later, I got a reply from my client saying this didn’t fix anything. But how could that be? It did work for Mark after all. Frustrated by the situation, I ordered [a Galaxy A13 from Back Market](https://www.backmarket.com/en-us/search?q=galaxy%20a13) so I could do all the tests I needed.

While I wait for my order to arrive, let’s talk about the <em>Samsung Email</em> app.

## Samsung Email with an Outlook Account

Do you know the acronym _GANGA_? It stands for “_Gmail App with a Non Gmail Account_” and refers to using the Google app (on either iOS or Android) with anything else than a Gmail account (for example with an Outlook.com address). This acronym is popular among email developers because in GANGA, you don’t get the same level of CSS support than with a regular Gmail account. (See my [Trying to make sense of Gmail CSS support](/2019/trying-to-make-sense-of-gmail-css-support-2019-edition/) post from 2019.)

Well, little did I know that there is also a… “_SEAWOA_”. Or “_Samsung Email App With an Outlook Account_”. (Okay, I just made that up. I hope it sticks.) If we use the _Samsung Email_ app with a “Hotmail Outlook Account”, we don’t get the same CSS support as for other types of accounts.

<figure class="figure">
<picture>
    <source srcset="/uploads/2023/12/samsung-email-account-setup-dark.jpg" media="(prefers-color-scheme:dark)" />
    <img loading="lazy" src="/uploads/2023/12/samsung-email-account-setup-light.jpg" alt="A screenshot from the Set up Email screen of the Samsung Email app." width="270" height="602" />
</picture>
</figure>

And one of the main difference in _SEAWOA_ is the lack of media query support. And guess who uses Outlook accounts for their entire company?

My client.

This now explains why the media query fix tried earlier didn’t work for my client (who uses an Outlook account) while it worked for Mark (who used a Gmail account).

Whatever fix I would find, it needed to work without media queries. To better understand what is going on, let’s dive deeper into the <em>Samsung Email</em> app.

## Auto-fit

Any Android app is distributed via an `.apk` file format, which is nothing more than a ZIP archive. Which is great, because it means we can snoop through the app to look for anything interesting. So I went to [APKMirror](https://www.apkmirror.com/apk/samsung-electronics-co-ltd/samsung-email/), searched for <em>Samsung Email</em> and downloaded the latest version APK.

As this was mentioned in Mosaico’s post I mentioned earlier, I knew that this whole feature was manager in an `AutoFit.js` file. Which again is great, because it means we can read the source code right away since JavaScript is not a pre-compiled language. So I read it.

And the least I can say is that it is quite… surprising. For example:

* Comments in the code reference specific emails, for example from Yahoo Finance or Youtube, and even mentions a specific Gmail account used internally by Samsung for their tests. I know not everyone reads JavaScript from unpackaged Android apps as a hobby, but this feels like this shouldn't be there.
* There are a lots of typos or spelling mistakes in variable names and functions, like `MOBILEDEIVCE`, `chagneSize` or `orignalDiv`. ([I poked fun at this one](https://mastodon.social/@HTeuMeuLeu/111545725122567328) on Mastodon as “<em>orignal</em>” is french for “<em>moose</em>”.)
* The code organization is clunky. For example, there is a `removeHeight100InNode` function called early on in the script that is supposedly there to replace any `height:100%` by `height:auto` throughout the HTML. (Interestingly, this is ignored if an element has a `position:relative`. I’m not sure what’s the reason for any of this.) But that same function is also responsible to determine the original maximum width of the email template.

And I think this is the heart of our problem here. Here’s an excerpt of the code from that function.

```js
var width = element.getAttribute("width");
if (width && width != "100%") {
    width = getMarginSize(width);
    if (width > originMaxWidth) {
        originMaxWidth = width;
    }
}

if (element.width && element.width != "100%") {
    width = getMarginSize(element.width);
    if (width > originMaxWidth) {
        originMaxWidth = width;
    }
}

if (element.style.width && element.style.width != "100%") {
    width = getMarginSize(element.style.width);
    if (width > originMaxWidth) {
        originMaxWidth = width;
    }
}
```

This code is run in a loop of every HTML elements in an email. And for every element, it tries to determine the width of that element based on these three things, in that order:

1. The `width` attribute.
2. The computed `width` in JavaScript.
3. The `width` style property.

At each step, it tries to assign the width found to an `originMaxWidth` variable (initially set to zero before the loop) _only_ if the width found is bigger than the current `originMaxWidth` value.

And that’s the problem, here. Because in HTML and CSS, it doesn’t matter if an element has a `width` attribute if it also has a `width` style set. The style value overtakes the attribute value. But with Samsung’s algorithm, the `width` attribute is actually more important. For example, consider the following code:

```html
<img src="hero.jpg" alt="" width="600" style="width:100%; max-width:600px;" />
```

This image, viewed in a `300px` container, would be `300px` wide. But with Samsung’s algorithm, this sets the `originMaxWidth` variable to `600px` because of the `width` attribute. And this triggers Samsung Email’s Auto-fit to display our email at a `600px` width, even though it was fluid and responsive in the first place.

The only solution to prevent this is to set the `width` attribute to `100%`. Easy enough, right?

But setting the `width` attribute in images to a value in pixels is actually a very good and important practice in HTML emails. _The Outlooks_ on Windows (versions 2016 and below) only understand the `width` attribute of an image. And if it is set to a percentage value, Outlook will understand this as “a percentage of the image’s physical width”, not a percentage of the image’s parent element as we would expect in CSS. So this means that if we have a `1200px` wide image called in Outlook 2016 with a `width="100%"` attribute, this image will be `1200px` wide.

## The fix

I received my Samsung device on tuesday and was able to confirm this behavior and make more tests. I then offered my client two possible solutions:

1. We use the `width="100%"` attribute on every large image. But **they** must make sure the image is at the physical width it will be displayed at in _The Outlooks_.
2. We duplicate the code for every large image and have one version with a `width` attribute value in pixels just for _The Outlooks_, and another version with a `width="100%"` attribute for every other email client. Something like this:

```html
<!--[if mso]>
<img src="hero.jpg" alt="" width="600" />
<![endif]-->
<!--[if !mso]><!-->
<img src="hero.jpg" alt="" width="100%" />
<!--<![endif]-->
```

They chose to go with the first solution. After some more tests and tweaks to make the template more _mobile first_ friendly, we finally had our emails responsive in _SEAWOA_!
