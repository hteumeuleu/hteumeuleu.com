---
title:  "Remaking Lynn Fisher’s responsive illustration in an HTML email"
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

# Exploration

TK

# Solution

The solution I ended up with relies on the properties `max-width` and  `background`. 

Let’s start with the left part of the top most face (the “_blue_” face).

```html
<div style="max-width:321px; min-height:428px; background:#26cbff url('left-blue.png') no-repeat right top;">
</div>
```

<figure class="figure--grid">
<img src="/uploads/2021/01/lynn-01.png" alt="" width="784" height="1021" />
<img src="/uploads/2021/01/lynn-02.png" alt="" width="784" height="1021" />
</figure>

Then, we wrap this with the second visible face (the skull).

```html
<div style="max-width:428px; background:#26cbff url('left-skull.png') no-repeat right top;">
    <div style="max-width:321px; min-height:428px; background:#26cbff url('left-blue.png') no-repeat right top;">
    </div>
</div>
```

<figure class="figure--grid">
<img src="/uploads/2021/01/lynn-03.png" alt="" width="784" height="1021" />
<img src="/uploads/2021/01/lynn-04.png" alt="" width="784" height="1021" />
</figure>

Then, we wrap this again with the third visible face (the pizza).

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
</figure>

And so on for every new face.

Here’s a minimal example with two faces (“_blue_” and “_skull_”) and the final center surprise.

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




# TK

- How the illustration works
- https://lynnandtonic.com/thoughts/entries/case-study-2019-refresh/
- Grid
- Flexbox
- display:inline-block

https://twitter.com/HTeuMeuLeu/status/1273284880047247366