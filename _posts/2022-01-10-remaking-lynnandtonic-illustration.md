---
title: "Remaking Lynn Fisher’s responsive illustration in an HTML email"
---

[Lynn Fisher’s 2019 homepage responsive illustration](https://lynnandtonic.com/archive/2019/) has to be one of my favorite web design example of the past few years. It is a series a self portraits nested in each others, only showing when [playing with the responsiveness](/uploads/2020/08/responsive-design.gif) of the page. I find it to be a perfect example of how design and code can work together to produce something original and meaningful.

<figure class="figure--large">
<a href="https://lynnandtonic.com/archive/2019/"><img src="/uploads/2020/08/lynnandtonic.png" alt="" width="1500" height="750" style="background-color:#24cbff;" /></a>
<figcaption>Screenshot from Lynn Fisher’s 2019 homepage at <a href="https://lynnandtonic.com/archive/2019/">LynnAndTonic.com</a>.</figcaption>
</figure>

Since I first saw this, I always wondered if this kind of responsive illustration could be done in an HTML email. Spoiler: [it totally can](https://twitter.com/HTeuMeuLeu/status/1273284880047247366). And here’s how I did it.

# How the original version works

Lynn Fisher has written [a great case study about this illustration](https://lynnandtonic.com/thoughts/entries/case-study-2019-refresh/) and I really invite you to read it. Basically, every face in the illustration is split in two (left and right). And each part is absolutely positioned in the page, revealing new faces using media queries and dedicated breakpoints.

Here’s an example of HTML code for the first three faces.

```html
<div class="face" id="blue">
    <img alt="" src="left-blue.svg" class="left" />
    <img alt="" src="right-blue.svg" class="right" />
</div>
<div class="face" id="skull">
    <img alt="" src="left-skull.svg" class="left" />
    <img alt="" src="right-skull.svg" class="right" />
</div>
<div class="face" id="pizza">
    <img alt="" src="left-pizza.svg" class="left" />
    <img alt="" src="right-pizza.svg" class="right" />
</div>
```

While this could work in one email client or another, media queries and absolute positioning are not really a good start to build an HTML email.

# How the email version works

I went in a lot of different directions to see how this effect could be reproduced without absolute positioning. I tried [using flexbox](https://lab.hteumeuleu.com/lynnandtonic/web-version/), [using images](https://github.com/hteumeuleu/email-lab/blob/master/lynnandtonic/img-version.html) with `srcset` and `sizes` to only show at specific breakpoints. I got closer [using tables and background images](https://github.com/hteumeuleu/email-lab/blob/master/lynnandtonic/table-version.html), only to realize this didn’t work in WebKit.

The solution I ended up is, in retrospect, the simplest and the most elegant. It only relies on the properties `max-width` and  `background`. 

Let’s start with the left part of the top most face (the “_blue_” face).

```html
<div style="max-width:321px; min-height:428px; background:#26cbff url('left-blue.png') no-repeat right top;">
</div>
```

Each face part is a `204×428px` image, right aligned to its container. We set this first outer most face part a little extra width (at `321px`) to give the whole thing extra horizontal margins on larger screens.

<figure class="figure--grid">
<img src="/uploads/2021/01/lynn-01.png" alt="" width="784" height="1021" />
<img src="/uploads/2021/01/lynn-02.png" alt="" width="784" height="1021" />
<figcaption>At a 180px wide viewport, the left part of the face is right aligned and truncated on its left. At a 360px wide viewport, the image is visible completely.</figcaption>
</figure>

Then, we wrap this with the second visible face (the skull). The image is still right aligned. But we only want that face slice to show at a maximum of `107px`. To do that, we set the `max-width` property to `107px` plus the size of all the previous slices before. Thus, `107` plus `321` equals `428px`.

```html
<div style="max-width:428px; background:#26cbff url('left-skull.png') no-repeat right top;">
    <div style="max-width:321px; min-height:428px; background:#26cbff url('left-blue.png') no-repeat right top;">
    </div>
</div>
```

<figure class="figure--grid">
<img src="/uploads/2021/01/lynn-03.png" alt="" width="784" height="1021" />
<img src="/uploads/2021/01/lynn-04.png" alt="" width="784" height="1021" />
<figcaption>At a 360px wide viewport, the second visible face (the skull) is partially visible. It is fully visible at a 540px wide viewport.</figcaption>
</figure>

Then, we wrap this again with the third visible face (the pizza). Same deal as before: a right aligned image, and a `max-width` of now `535px` (`428` from the last slices plus `107` from this new one).

```html
<div style="max-width:535px; background:#26cbff url('left-pizza.png') no-repeat right top;">
    <div style="max-width:428px; background:#26cbff url('left-skull.png') no-repeat right top;">
        <div style="max-width:321px; min-height:428px; background:#26cbff url('left-blue.png') no-repeat right top;">
        </div>
    </div>
</div>
```

<figure>
<img src="/uploads/2021/01/lynn-05.png" alt="" width="1504" height="1021" />
<figcaption>Showing three fully visibles left-part faces at a 720px viewport.</figcaption>
</figure>

And so on for every new face. The last slice changes a little bit with a wider image and a repeating pattern. (But I won’t spoil it to you, so [go check it out](https://lab.hteumeuleu.com/lynnandtonic/).)

And then we repeat the same structure for the right part of all the faces, except this time each image is aligned to the left of its container.

Here’s [a minimal example](/uploads/2022/01/lynnandtonic-minimal-example.html) with two faces (“_blue_” and “_skull_”) and the final center surprise.

```html
<div style="background:#26cbff url('lynn-pattern.png') repeat-x center top;">
    <div style="font-size:0; text-align:center; background:url('lynn-armstrong.png') no-repeat center top;">
        <div style="display:inline-block; width:50%;">
            <div style="max-width:428px; background:#26cbff url('left-skull.png') no-repeat right top;">
                <div style="max-width:321px; min-height:428px; background:#26cbff url('left-blue.png') no-repeat right top;">
                </div>
            </div>
        </div>
        <div style="display:inline-block; width:50%;">
            <div style="margin:0 0 0 auto; max-width:428px; background:#26cbff url('right-skull.png') no-repeat left top;">
                <div style="margin:0 0 0 auto; max-width:321px; min-height:428px; background:#26cbff url('right-blue.png') no-repeat left top;">
                </div>
            </div>
        </div>
    </div>
</div>
```

# Conclusion

Here’s [the final version](https://lab.hteumeuleu.com/lynnandtonic/) and [a mirror on CodePen](https://codepen.io/hteumeuleu/pen/LYGbzdQ). Considering it only runs on `max-width` and `background-image`, [support is really great](https://www.caniemail.com/search/?s=css-background-image+%2B+css-max-width). It works in Gmail (desktop webmail, mobile apps), Outlook.com, Yahoo! Mail (desktop webmail, mobile apps), etc.

<figure class="figure--large">
    <iframe height="300" style="width: 100%;" scrolling="no" title="Remaking @lynnandtonic's responsive illustration in an HTML email" src="https://codepen.io/hteumeuleu/embed/preview/LYGbzdQ?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
      See the Pen <a href="https://codepen.io/hteumeuleu/pen/LYGbzdQ">
      Remaking @lynnandtonic's responsive illustration in an HTML email</a> by Rémi (<a href="https://codepen.io/hteumeuleu">@hteumeuleu</a>)
      on <a href="https://codepen.io">CodePen</a>.
    </iframe>
</figure>

It’s missing a few details from Lynn Fisher’s original work (like the subtle shades, the progressive scaling of each slice appearing, and, sorry about that… _the dog_).

It doesn’t work in _The Outlooks_. I started [a VML version](https://lab.hteumeuleu.com/lynnandtonic/vml-version.html) but didn't get very far. I am wondering if it could work that way. Let me know if you get to anything.

I worked in this right after stumbling upon Lynn Fisher’s portfolio at the end of 2019. It took me a few months of back and forth and shower thoughts. I [posted this on Twitter](https://twitter.com/HTeuMeuLeu/status/1273284880047247366) on June 17th, 2020 and got great feedback.

Then I started writing this post but procrastinated up until now. I hope you enjoyed it anyway!