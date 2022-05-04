---
title:  "The email rendering process"
---

## 1. Your HTML

Your shiny, glorious HTML.

May you code manually in HTML, use a pseudo-language like MJML or HEML, or a framework like Maizzle, what you send is HTML.

## 2. Your ESP

Salesforce Marketing Cloud, Mailchimp, Mailjet, Sendgrid, …

## 3. The recipient’s email server

Exchange is notorious for removing `<style>` tags and media queries. It doesn't matter which email client you’re using next, the final email received will be altered.

This can also happen with proxy email services, like Apple Relay or Firefox Relay. 

## 4. The email client

Gmail desktop webmail, Outlook iOS app, …

This can happen server side or client side. For example, every OAuth clients (Yahoo, AOL) share the same server side rendering process. But then each client has it overlying quirks (for example, Yahoo Android app doesn't support the first `<head>`).

## 5. The rendering engine

WebKit, Blink, Gecko, …

Sadly the world is dominated by Blink (used in Chrome, Edge, Opera, Vivaldi, Brave, …), which itself is derived from WebKit, which itself is derived from KHTML, which itself is derived from the _khtmlw_ library, and so on.

For example, Gmail supports `background-clip:text`. But this only works under WebKit’s rendering engine. Blink only supports this under the `-webkit-` prefix.

https://github.com/hteumeuleu/email-bugs/issues/68#issuecomment-1077531703

## 6. User’s preference

font, font-size, zoom, dark mode, high-contrast mode, …
