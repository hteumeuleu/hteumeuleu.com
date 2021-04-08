---
title:  "Fixing Gmail’s dark mode issues with CSS Blend Modes"
---

Since its debut in october 2019, Gmail’s dark mode has been causing [a lot of headaches](https://github.com/hteumeuleu/email-bugs/issues/68). It has improved and standardized over time, but there are still glaring differences between Gmail’s dark mode in iOS versus Android.

One of the most inconvenient problem in iOS in particular is that Gmail insists on changing any light text color to a dark text color. So an already dark email with white text on a black background will turn black on white. Not only does that seem counter-efficient, but it also creates real accessibility and readibility issues.

Take [this email from Nest](https://reallygoodemails.com/emails/black-friday-deals-are-here) for example.

<figure class="figure--grid">
    <img alt="" src="/uploads/2021/04/nest-email-gmail-light-mode.png" width="375" height="667">
    <img alt="" src="/uploads/2021/04/nest-email-gmail-dark-mode.png" width="375" height="667">
    <figcaption>A screenshot of an already dark email from Nest in Gmail iOS in light mode (left) and dark mode (right). In dark mode, the already dark background colors are turned into light colors. The white text “Nest” included in the logo image becomes almost invisible in dark mode.</figcaption>
</figure>

I’ve been thinking for a while about how to solve this. And CSS blend modes have been in my mind [ever since they’ve been supported in Gmail](https://twitter.com/HTeuMeuLeu/status/784480676246593537). So here’s how to fix (some of) Gmail’s dark mode issues with CSS Blend Modes.

## 1. The base code

For this example, we’ll start by using a single `<div>` with a black background and white text. 

```html
<div style="background:#000; color:#fff;">
    Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
</div>
```

## 2. How Gmail transforms colors

If you open an email with the code above in Gmail iOS in dark mode, you’ll see that the colors have been changed. It’s never been clear to me how Gmail _actually_ proceeds to do these color changes. (You still get your original colors if you try to copy and paste the email or if you forward it.) But the end result would be similar to the code below.

```html
<div style="background:#fff; color:#000;">
    Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
</div>
```

## 3. Adding blend modes in the mix

Let’s go back to our initial code and let the magic of blend modes begin. We will add two inner `<div>` containers, each with a black background and a different blending mode.

```html
<div style="background:#000; color:#fff;">
    <div style="background:#000; mix-blend-mode:screen;">
        <div style="background:#000; mix-blend-mode:difference;">
            Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
        </div>
    </div>
</div>
```

And as our final trick, we’ll use a background image created with a `linear-gradient` in CSS to maintain our desired background color. (I learnt this hack from [Annett Forcier](https://twitter.com/The_Annett) during [her dark mode webinar](https://www.emailonacid.com/resource/webinar-dark-mode-email-design/) with [Anne Tomlin](https://twitter.com/pompeii79) last year.)

```html
<div style="background:#000; background-image:linear-gradient(#000,#000); color:#fff;">
    <div style="background:#000; mix-blend-mode:screen;">
        <div style="background:#000; mix-blend-mode:difference;">
            Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
        </div>
    </div>
</div>
```

Let's see step by step how this is processed in Gmail iOS and why this works.

### 3.1 How Gmail changes this code

First, Gmail will change the colors. White text will turn into black and black backgrounds will turn into white, except for the `linear-gradient` one. So our code will end up to something like this.

```html
<div style="background:#fff; background-image:linear-gradient(#000,#000); color:#000;">
    <div style="background:#fff; mix-blend-mode:screen;">
        <div style="background:#fff; mix-blend-mode:difference;">
            Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
        </div>
    </div>
</div>
```

### 3.2 Blend modes

Let’s talk about blend modes. [Per definition](https://developer.mozilla.org/en-US/docs/Web/CSS/blend-mode):

> _A blend mode describes how colors should appear when elements overlap._ 

A blend operation takes a “_source color_” and blends it with a “_backdrop color_”. The exact result depends on the type of blend mode we use. 

We can use blend modes [in Photoshop](https://helpx.adobe.com/photoshop/using/blending-modes.html). And we can use blend modes in CSS thanks to two properties: [`background-blend-mode`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-blend-mode) and [`mix-blend-mode`](https://developer.mozilla.org/en-US/docs/Web/CSS/mix-blend-mode). Both are [equally supported in Gmail](https://www.caniemail.com/search/?s=blend-mode). But because our primary goal here is to maintain text color, we’ll only use `mix-blend-mode`.

If you want to learn more about blend modes in CSS, I highly recommend to read the following articles:

* [Blending Modes in CSS](https://ishadeed.com/article/blending-modes-css/), by Ahmad Shadeed
* [Compositing And Blending In CSS](https://www.sarasoueidan.com/blog/compositing-and-blending-in-css/), by Sara Soueidan
* [Taming Blend Modes: `difference` and `exclusion` ](https://css-tricks.com/taming-blend-modes-difference-and-exclusion/), by Ana Tudor

### 3.3 The first blend: `difference`

The first blend that occurs in our code is a `difference`. By the [W3C specification](https://drafts.fxtf.org/compositing-1/#valdef-blend-mode-difference) definition, a `difference` blend mode “_subtracts the darker of the two constituent colors from the lighter color_”. Mathematically, the result is the absolute value of the difference between our source color (`Cs`) and our backdrop color (`Cb`).

```js
B(Cb, Cs) =|Cb - Cs|
```

If we get back to our code, this blend mode will occur between our black text on a white background (as transformed by Gmail) and its parent white background.

```html
<div style="background:#fff;">
    <div style="background:#fff; mix-blend-mode:difference;">
        Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
    </div>
</div>
```

Mathematically, this means that our black text from the source () becomes…

```js
|Cb - Cs| = |rgb(255,255,255) - rgb(0,0,0)|
          = rgb(255,255,255)
```

…white! And the white background from that source blended with the white background from the backdrop becomes…

```js
|Cb - Cs| = |rgb(255,255,255) - rgb(255,255,255)|
          = rgb(0,0,0)
```

…black!

Perfect! But wait, what happens if this applies in Gmail in light mode?

```html
<div style="background:#000;">
    <div style="background:#000; color:#fff; mix-blend-mode:difference;">
        Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
    </div>
</div>
```

Our white text source blended with the black backdrop becomes…

```js
|Cb - Cs| = |rgb(0,0,0) - rgb(255,255,255)|
          = rgb(255,255,255)
```

…white! And our black background source blended with the black backdrop becomes…

```js
|Cb - Cs| = |rgb(0,0,0) - rgb(0,0,0)|
          = rgb(0,0,0)
```

…black! It still works! But wait again, aren’t we done here? If all you need is white text on a black background, you can indeed stop right there. But if you need white text on a colored background, we’re going to need that second blend.

### 3.4 The second blend: `screen`

Let’s go back a bit and start again with a white text on a purple background (`#639`).

```html
<div style="background:#639; background-image:linear-gradient(#639,#639); color:#fff;">
    <div style="background:#000; mix-blend-mode:screen;">
        <div style="background:#000; mix-blend-mode:difference;">
            Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
        </div>
    </div>
</div>
```

First, Gmail will change those colors. Black becomes white, white becomes black, and the purple becomes a slightly lighter purple.

```html
<div style="background:#c7a4ef; background-image:linear-gradient(#639,#639); color:#000;">
    <div style="background:#fff; mix-blend-mode:screen;">
        <div style="background:#fff; mix-blend-mode:difference;">
            Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
        </div>
    </div>
</div>
```

Then, the first blend (`difference`) applies. But at this stage, we’ll get the exact same result as before: a white text on a black background.

Thus enters our second blend: `screen`. By the [W3C specification](https://drafts.fxtf.org/compositing-1/#blendingscreen) definition, a `screen` blend mode “_multiplies the complements of the backdrop and source color values, then complements the result_”. Yeah, I didn't get that either. But the maths here helped me to make sense of it.

```js
B(Cb, Cs) = 1 - [(1 - Cb) * (1 - Cs)]
          = Cb + Cs - (Cb * Cs)
```

So let’s see how this goes. At this stage, it’s just as if we’d blend a white text color on a black background with our purple backdrop.

```html
<div style="background:#639;">
    <div style="background:#000; color:#fff; mix-blend-mode:screen;">
        <div>
            Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
        </div>
    </div>
</div>
```

Our white text source (`Cs`) blended with the purple backdrop (`Cb`) becomes…

```js
B(Cb, Cs) = Cb + Cs - (Cb * Cs)
          = rgb(102, 51, 153) + rgb(255,255,255) - (rgb(102, 51, 153) * rgb(255,255,255))
          = rgb(102, 51, 153) + rgb(255,255,255) - rgb(102, 51, 153)
          = rgb(255,255,255)
```

…white! And our black background source blended with the purple backdrop becomes…

```js
B(Cb, Cs) = Cb + Cs - (Cb * Cs)
          = rgb(102, 51, 153) + rgb(0,0,0) - (rgb(102, 51, 153) * rgb(0,0,0))
          = rgb(102, 51, 153) + rgb(0,0,0) - rgb(0,0,0)
          = rgb(102, 51, 153)
```

…purple! And voilà, this is how you maintain a white text on any background color. And this also works with background images.

```html
<div style="background:#6db6b0; background-image:url(https://i.imgur.com/F7S1NGq.jpg); background-size:cover; color:#fff;">
    <div style="background:#000; mix-blend-mode:screen;">
        <div style="background:#000; mix-blend-mode:difference;">
            Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
        </div>
    </div>
</div>
```

The only problem here is that this code would apply to any email client, including those that don’t support CSS blend modes. We need to make sure that this code only applies in Gmail.

## 4. Targeting Gmail

TODO

[HowToTarget.email](https://howtotarget.email/)

```
u + .body .foo
```

## 5. Final code

TODO

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fixing Gmail’s dark mode issues with CSS Blend Modes</title>
    <style>
        u + .body .gmail-blend-screen { background:#000; mix-blend-mode:screen; }
        u + .body .gmail-blend-difference { background:#000; mix-blend-mode:difference; }
    </style>
</head>
<body class="body">
    <div style="background:#639; background-image:linear-gradient(#639,#639); color:#fff;">
        <div class="gmail-blend-screen">
            <div class="gmail-blend-difference">
                Lorem ipsum dolor, sit amet, consectetur adipisicing elit.
            </div>
        </div>
    </div>
</body>
</html>
```

## Conclusion

TODO