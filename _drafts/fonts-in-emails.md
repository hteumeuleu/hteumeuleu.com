---
title:  "Fonts in HTML emails"
---

There is no such thing as a “safe font” in HTML emails. There, I said it. I’ve seen so many posts asking for safe fonts to use in HTML emails, usually answered with a reduced list of fonts available on Windows. So let me explain why safe fonts is a deprecated concept and how much more there can be to fonts in HTML emails.

Fonts in an HTML email can come from **four different sources**:

1. System fonts
2. User fonts
3. Email Client fonts
4. Embedded fonts

## 1. System Fonts

Every operating system comes with a default set of fonts available throughout the entire system. Here are links to the full list of available fonts provided by system vendors.

* [macOS Catalina and iOS 13](https://developer.apple.com/fonts/system-fonts/)
* [Windows 10](https://docs.microsoft.com/en-us/typography/fonts/windows_10_font_list)
* [Android](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/data/fonts/fonts.xml)

macOS Catalina provides a staggering amount of 520 preinstalled fonts. iOS 13 follows with 273 preinstalled fonts. Windows 10 has 183 fonts. And Android has… 9 font families.

Android is a special case because there’s not one true version of Android. Default fonts will vary if you have a Samsung, Xiaomi, … device. While Apple and Microsoft provide very clear lists of the default fonts installed on their systems, I haven't been able to find such a list for any maker of Android devices. (Google only has a list from way back to [Android 4.1](https://developer.android.com/about/versions/android-4.1.html#Fonts)).

With so little fonts available on Android, it should be clear now why “safe fonts” make little to no sense. Even _Arial_ or _Times New Roman_ are absent from Android.

## 2. User Fonts

You can install your own fonts in any system these days. Download a font you like from [Google Fonts](https://fonts.google.com), [Adobe](https://fonts.adobe.com/), [DaFont](https://www.dafont.com/) or whatever is your favorite foundry, install it, and it now becomes available throughout your entire system.

While this has been possible pretty much since dawn in operating systems like Windows, macOS or Android, it’s only been possible [since iOS 13](https://support.apple.com/en-us/HT210393#:~:text=Custom%20fonts). For example, you can install [Adobe Creative Cloud](https://apps.apple.com/us/app/adobe-creative-cloud/id852473028) on iOS and download and install any new font from there, making it available in any other iOS app.

## 3. Email Client Fonts

Email clients can also come with their own fonts. This is true for native applications, but also for any web email client. For example, Gmail’s desktop webmail embeds its own version of _Google Sans_ (in Regular, Medium) and _Roboto_ (in Regular, Medium, Bold). Outlook.com’s desktop webmail embeds Segoe UI Web (in Regular, Semilight, Semibold) and the [Shell Fabric MDL2 Icons](https://docs.microsoft.com/en-us/windows/uwp/design/style/segoe-ui-symbol-font) icon font.

## 4. Embedded Fonts

<iframe src="https://embed.caniemail.com/css-at-font-face/" width="640" height="420" style="width:100%; max-width:640px; border:none;" loading="lazy"></iframe>

---