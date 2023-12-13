---
title:  "Samsung Auto-fit"
---

https://mosaico.io/email-client-tricks/samsung-email-autowidth-breaks-repositivity/
https://github.com/hteumeuleu/email-bugs/issues/73

* Samsung app doesn’t support media queries with a Hotmail/Outlook Account.
* Samsung Auto-Fit algorithm is faulty at best. They look for an image’s `width` attribute first and ignore any style afterwards. Thus forcing a desktop layout.
* Samsung's autofit script is… terrible. Bad variables and functions naming. Comments related to specific emails and leaking a personal test email address.
* TK: The fix with a Gmail account should be enough with just the following.

```html
<style>
	@media only screen and (max-device-width:384px) { /* Hello Samsung! */ }
</style>
```
(This doesn't work at all.)

* The fix for non Gmail account absolutely requires image's width attribute to be at 100%. So either make sure your images are correctly sized. Or duplicate your image tag in two mso/!mso versions.
* Bought a device on Backmarket for testing.
* Thanks to Mark Robbins for the support and help on this.
* An APK is just a zip file so we can inspect files within and the Autofit script.
