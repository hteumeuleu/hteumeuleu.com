---
title: "Fonts in HTML emails"
---

There is no such thing as a “web safe font”. There, I said it. I’ve seen so many posts asking for safe fonts to use in HTML emails, usually answered with a reduced list of fonts available on Windows. So let me explain why safe fonts is a fallacious concept and how much more there can be to fonts in HTML emails than _Arial_ and _Times_.

Fonts in an HTML email can come from **four different sources**:

1. System fonts
2. User fonts
3. Email Client fonts
4. Embedded fonts

Let’s talk about each of those.

## 1. System Fonts

Every operating system comes with a default set of fonts available throughout the entire system. Here are links to the full list of available fonts provided by system vendors.

* [macOS Catalina and iOS 13](https://developer.apple.com/fonts/system-fonts/)
* [Windows 10](https://docs.microsoft.com/en-us/typography/fonts/windows_10_font_list)
* [Android](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/data/fonts/fonts.xml)

macOS Catalina provides a staggering amount of 520 preinstalled fonts. iOS 13 follows with 273 preinstalled fonts. Windows 10 has 183 fonts. And Android has… 9 font families.

Android is a special case because there’s not one true version of Android. Default fonts will vary if you have a Samsung, Xiaomi, or Google device. While Apple and Microsoft provide very clear lists of the default fonts installed on their systems, I haven't been able to find such a list for any maker of Android devices. (Google only has a list from way back to [Android 4.1](https://developer.android.com/about/versions/android-4.1.html#Fonts)).

With so little fonts available on Android, it should be clear now why talking about “safe fonts” make little to no sense. Even _Arial_ or _Times New Roman_ are absent from Android, thus falling back to respectively _Roboto_ and _Noto Serif_.

## 2. User Fonts

You can install your own fonts in any system these days. Download a font you like from [Google Fonts](https://fonts.google.com), [Adobe](https://fonts.adobe.com/), [DaFont](https://www.dafont.com/) or whatever is your favorite foundry, install it, and it now becomes available throughout your entire system.

While this has been possible pretty much since dawn in operating systems like Windows, macOS or Android, it’s only been possible in iOS [since iOS 13](https://support.apple.com/en-us/HT210393#:~:text=Custom%20fonts) (in 2019). For example, you can install [Adobe Creative Cloud](https://apps.apple.com/us/app/adobe-creative-cloud/id852473028) on iOS and download and install any new font from there, making it available in any other iOS app.

## 3. Email Client Fonts

Email clients can also come with their own fonts. This is true for native applications, but also for any web email client. For example, Gmail’s desktop webmail embeds its own version of _Google Sans_ (in Regular, Medium) and _Roboto_ (in Regular, Medium, Bold). Outlook.com’s desktop webmail embeds _Segoe UI Web_ (in Regular, Semilight, Semibold) and the _[Shell Fabric MDL2 Icons](https://docs.microsoft.com/en-us/windows/uwp/design/style/segoe-ui-symbol-font)_ icon font.

## 4. Embedded Fonts

Email developers can embed fonts from a distant server on their HTML emails, just like on the web. Does it work everywhere? [No](https://www.caniemail.com/features/css-at-font-face/), [just like on the web](https://caniuse.com/fontface).

<iframe title="Can I email… @font-face" src="https://embed.caniemail.com/css-at-font-face/" width="640" height="420" style="width:100%; max-width:40rem; height:26.25rem; border:none;" loading="lazy"></iframe>

Why don’t more email clients support embedded fonts? I can’t speak for them. But my guess is it all comes down to security. Font files are nothing more than little executable software in themselves. And just like any software, they’re vulnerable and can thus bring their vulnerabilities into any software that embeds them. ([See this StackExchange thread](https://security.stackexchange.com/questions/91347/how-can-a-font-be-used-for-privilege-escalation) for a lot of examples.)

## Best practices for setting fonts in HTML emails

* The `font` shorthand property has [very good support](https://www.caniemail.com/features/css-font/). So instead of declaring individual properties (like `font-family:sans-serif; font-size:16px; line-height:24px; font-weight:bold;`), you can declare a single `font` property (like `font:bold 16px/24px sans-serif`). It’s shorter, cleaner. I like it.
* Always declare a generic font family name as a fallback. If you omit it, the rendering engine will use whatever is the default font. For example, in a modern browser, `font-family:Lobster` will render as `Times` while `font-family:Lobster, sans-serif` will render as either Arial, Helvetica or Roboto depending on your system.
* If you use a `@font-face` declaration, include the properties `mso-generic-font-family` and `mso-font-alt` to better control the fallback font in _The Outlooks_ on Windows. (You can [read about the day I learned about `mso-generic-font-family`](/2019/today-i-learned-about-mso-generic-font-family/).)
* [Font Style Matcher](https://meowni.ca/font-style-matcher/) by [Monica Dinculescu](https://twitter.com/notwaldorf) is a great tool to find a fallback font not too different from the web font you want to use.
* [Font Family Reunion](http://fontfamily.io/) by [Zach Leat](https://twitter.com/zachleat) is a great tool to see which system font you’ll get from a `font-family` font stack.

<div class="alert">
   This post was published today in french as part of “<a href="https://www.24jours.email" lang="fr" hreflang="fr"><em>24jours.email</em></a>”, an advent calendar blog on email marketing. Read “<a href="https://www.24jours.email/2020/11/18/les-polices-dans-les-e-mails-html/" lang="fr" hreflang="fr"><em>Les polices dans les e-mails HTML</em></a>” and check out the rest of the calendar!
</div>