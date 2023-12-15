---
title:  "Samsung Auto-fit"
---

This week, I got bit pretty hard by a bug I somehow managed to completely avoid until now. This bug involves three ingredients: the [Samsung Email](https://play.google.com/store/apps/details?id=com.samsung.android.email.provider) app on Android, its <em>Auto-fit</em> feature, and an Outlook account. Here’s everything I learnt about this.

It all started on a friday afternoon as I received an email from a client I’ve been working with in the past few months. Apparently, the fluid/hybrid templates I’ve carefully hand coded were rendering as the desktop version in the Samsung Email app on Android. And of course, the main boss of the organization used a Samsung phone with the <em>Samsung Email</em> app. So I had to fix this.

[TODO: screenshot]

My first problem was… The <em>Samsung Email</em> app can only be installed on a Samsung device. And I didn’t have any Samsung device at hand. And the Samsung app isn’t part of Email of Acid or Litmus either. So I did what any email developer should do in this situation: ask for help in [the #emailgeeks Slack](https://email.geeks.chat/). 

[TODO: screenshot]

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

While I wait for my order to arrive, it was a good time to dive deeper into the <em>Samsung Email</em> app.

## Auto-fit

Any Android app is distributed via an `.apk` file format, which is nothing more than a ZIP archive. Which is great, because it means we can snoop through the app to look for anything interesting. So I went to [APKMirror](https://www.apkmirror.com/apk/samsung-electronics-co-ltd/samsung-email/), searched for <em>Samsung Email</em> and downloaded the latest version APK.

As this was mentioned in Mosaico’s post I mentioned earlier, I knew that this whole feature was manager in an `AutoFit.js` file. Which again is great, because it means we can read the source code right away since JavaScript is not a pre-compiled language. So I read it.

And the least I can say is that it is quite… unusual. For example:

* Comments in the code reference specific emails, for example from Yahoo Finance or Youtube, and even mentions specific Gmail accounts used internally by Samsung for their tests. I know not everyone reads JavaScript from unpackaged Android apps as a hobby. But this feels like this shouldn't be there.
* There are a lots of typos or spelling mistakes in variable names and functions, like `MOBILEDEIVCE` or `orignalDiv`. ([I poked fun at this one](https://mastodon.social/@HTeuMeuLeu/111545725122567328) on Mastodon as “<em>orignal</em>” is french for “<em>moose</em>”.)

* Samsung Auto-Fit algorithm is faulty at best. They look for an image’s `width` attribute first and ignore any style afterwards. Thus forcing a desktop layout.
* Samsung's autofit script is… terrible. Bad variables and functions naming. Comments related to specific emails and leaking a personal test email address.

## Samsung Email with a Hotmail/Outlook Account

You knew GANGA? Well, here’s SEHOA. 

* Samsung app doesn’t support media queries with a Hotmail/Outlook Account.

## The fix

* The fix for non Gmail account absolutely requires image's width attribute to be at 100%. So either make sure your images are correctly sized. Or duplicate your image tag in two mso/!mso versions.
