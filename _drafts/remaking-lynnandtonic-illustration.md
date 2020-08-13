---
title:  "Remaking Lynn Fisher’s responsive illustration in an HTML email"
---

[Lynn Fisher’s homepage responsive illustration](https://lynnandtonic.com/) has to be one of my favorite web design example of the past few years. It is a series a self portraits presented as Russian nested dolls, only showing when [working with the responsiveness](/uploads/2020/08/responsive-design.gif) of the page. I find it to be a perfect example of how design and code can work together to produce something original and meaningful.

<figure class="figure--large">
<a href="https://www.lynnandtonic.com"><img src="/uploads/2020/08/lynnandtonic.png" alt="" width="1500" height="750" style="background-color:#24cbff;" /></a>
<figcaption>Screenshot from Lynn Fisher’s homepage at <a href="https://www.lynnandtonic.com">LynnAndTonic.com</a>.</figcaption>
</figure>

Since I first saw this, I always wondered is this kind of responsive illustration could be done in an HTML email. Spoiler: [it totally can](https://twitter.com/HTeuMeuLeu/status/1273284880047247366). And here’s how I did it.

# How the original version works

Lynn Fisher has written [a great case study about this illustration](https://lynnandtonic.com/thoughts/entries/case-study-2019-refresh/) and I really invite you to read it. Basically, every face in the illustration is split in two (left and right). And each part is absolutely positioned in the page, revealing new faces using media queries and dedicated breakpoints.

```
<div class="face" id="mustache">
    <img src="left-blue.svg" class="left" />
    <img src="right-blue.svg" class="right" />
</div>
```

# TK

- How the illustration works
- https://lynnandtonic.com/thoughts/entries/case-study-2019-refresh/
- Grid
- Flexbox
- display:inline-block

https://twitter.com/HTeuMeuLeu/status/1273284880047247366