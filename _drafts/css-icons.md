---
title:  "Drawing CSS icons for fun… and dark mode"
---

Last year, I offered my help to redesign one of my favorite newsletter: [Switch Weekly](https://switchweekly.com/), curated by [Chris Brandrick](https://mastodon.social/@brandrick). As a Nintendo fan, I figured this was perhaps the closest I could ever get to work on a Nintendo related newsletter. This was also a great opportunity to try and experiment new things without a dreadly deadline.

I wanted to add little sparks of fun throughout the newsletter. And I thought about using an icon before the start of the introduction.

![A screenshot of the newsletter intro with a newspaper icon right before the text.](/uploads/2023/02/switch-weekly-icon.png)

This looked simple and nice enough. Except when you viewed it in dark mode.

![The same screenshot as before but now in dark mode. The dark outlined icon is now dark on a dark background.](/uploads/2023/02/switch-weekly-icon-buggy.png)

The *by default* dark icon on a light background now became dark on a dark background, making it almost invisible. Now I could make a second light version of the icon, wrap it in an `srcset` in a `<picture>` element bound to a dark mode media query. And I could use `data-ogsb` attributes to provide additional support in Outlook.com and Outlook mobile apps. But this would end up in a behemoth of code that would still fail in some key clients (hello Gmail apps!). (You can see [an example of this technique here](https://codepen.io/hteumeuleu/pen/porJdjR).)

So I wondered if this little icon could be drawn in CSS.

## CSS Drawings

I’ve always been in awe in front of CSS drawings. I’m admirative of the cuteness and clever techniques of [Lynn Fisher](https://lynnandtonic.com/)’s [A Single Div](https://a.singlediv.com/) for example. And I’m frankly mind blown by the work of [Diana Smith](https://diana-adrianne.com/) in her *pure CSS* art like [Francine](https://diana-adrianne.com/purecss-francine/). But I’ve never found any real use case for these myself.

<figure class="figure">
<a href="https://diana-adrianne.com/#portfolio"><img src="/uploads/2023/02/diana-smith-css-art.jpg" alt="" width="1253" height="835" /></a>
<figcaption>A gallery of HTML/CSS drawings by Diana Smith. Yes, this was done with just HTML and CSS.</figcaption>
</figure>

The techniques involved feel very intimidating to me: CSS transforms, CSS gradients, absolute positioning… This almost feels like the list of things not usually supported by email clients.

But my little newspaper icon was simple enough that I felt I could do it with more basic styles.

## How I did it

My first attempt consisted in a bunch of nested `<span>`. I went with `<span>`s to keep the semantics neutral, all while implying this would be usable as an `inline` element. I added a `role="presentation"` to the main wrapping `<span>`, considering this icon to be completely decorative and not requiring a proper textual alternative. I used one `<span>` for each shape within the icon, eventually grouping two or more in another `<span>` when it made things easier to layout.

<figure class="figure">
<img src="/uploads/2023/03/css-icon-firefox-devtools.png" alt="" width="1024" height="768" />
<figcaption>A screenshot of my CSS icon in Firefox with devtools opened.</figcaption>
</figure>

One thing I wanted early on was to make sure the CSS icon could scale to any size depending on the current `font-size`. So I applied a `font-size:1em` to every inner element. I then used only `em` units inside the icon, may it be for `width`, `height`, `padding` or `border`. So for example, having a border of `0.1em` with a current `font-size` of `20px` would make the border `2px` large. Increasing the `font-size` to `40px` would automatically scale that border to `4px`.

Then I used a mix of basic CSS properties to layout the different shapes within the icon: `display`, `width`, `height`, `margin`, `padding`, `border` and `background-color`. At this point the entire code for the CSS icon looked like this.

```html
<span style="margin-right:0.2em; font-size:1.25em; display:inline-block; vertical-align:middle;" role="presentation">
	<span style="font-size:1em; display:block; border:0.1em solid #000; border-radius:0 0 0.1em 0.1em;">
		<span style="font-size:1em; display:block; padding:0.1em 0.1em 0.25em;">
			<span style="font-size:1em; display:table;">
				<span style="font-size:1em; display:table-cell; width:0.2em; height:0.25em; background:#000;"></span>
				<span style="font-size:1em; display:table-cell; padding-left:0.05em;">
					<span style="font-size:1em; display:block; margin-bottom:0.05em; width:0.35em; height:0.1em; background:#000;"></span>
					<span style="font-size:1em; display:block; width:0.35em; height:0.1em; background:#000;"></span>
				</span>
			</span>
		</span>
	</span>
</span>
```

The problem with mixing `border` (used for the icon’s outer border) and `background` (used for each individual internal shapes) colors in CSS is that they’re not treated equally when it comes to dark mode. In Outlook.com for example, `border` colors are always left untouched while text `color` or `background-color` are forced to different colors in dark mode.

<figure class="figure">
<img src="/uploads/2023/03/icon-in-outlook-com.png" alt="" width="768" height="240" />
<figcaption>A screenshot of the Switch Weekly newsletter viewed in dark mode in Outlook.com.</figcaption>
</figure>

This is where I thought about using `currentColor`. This CSS keyword “_represents the value of an element's `color` property_” (according to [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#currentcolor_keyword)).

```html
<span style="color:#000; margin-right:0.2em; font-size:1.25em; display:inline-block; vertical-align:middle;" role="presentation">
	<span style="font-size:1em; display:block; border:0.1em solid; border-radius:0 0 0.1em 0.1em;">
		<span style="font-size:1em; display:block; padding:0.1em 0.1em 0.25em;">
			<span style="font-size:1em; display:table;">
				<span style="font-size:1em; display:table-cell; width:0.2em; height:0.25em; background:currentColor;"></span>
				<span style="font-size:1em; display:table-cell; padding-left:0.05em;">
					<span style="font-size:1em; display:block; margin-bottom:0.05em; width:0.35em; height:0.1em; background:currentColor;"></span>
					<span style="font-size:1em; display:block; width:0.35em; height:0.1em; background:currentColor;"></span>
				</span>
			</span>
		</span>
	</span>
</span>
```
Final:

```html
<span style="color:#000; margin-right:0.2em; font-size:1.25em; display:inline-block; vertical-align:middle;" role="presentation">
	<span style="font-size:1em; display:block; border:0.1em solid; border-radius:0 0 0.1em 0.1em;">
		<span style="font-size:1em; display:block; padding:0.1em 0.1em 0.25em;">
			<span style="font-size:1em; display:table;">
				<span style="font-size:1em; display:table-cell; width:0.2em; border-bottom:0.25em solid;"></span>
				<span style="font-size:1em; display:table-cell; padding-left:0.05em;">
					<span style="font-size:1em; display:block; margin-bottom:0.05em; width:0.35em; border-bottom:0.1em solid;"></span>
					<span style="font-size:1em; display:block; width:0.35em; border-bottom:0.1em solid;"></span>
				</span>
			</span>
		</span>
	</span>
</span>
```

* Using `background` fails as the color changes with no control. We lack a `currentbackgroundcolor` keyword in CSS.
* Using borders can be a problem as they are not changed in dark mode in Outlook.
* But using borders with `currentcolor` solves the problem!


## Principles for coding CSS icons in HTML Emails

* Set `font-size:1em` on every single element.
* Restrict properties to `display`, `width`, `height`, `margin`, `padding`, `border`.
* Do not define colors.

---

https://codepen.io/hteumeuleu/pen/porJdjR

https://github.com/hteumeuleu/switchweekly

https://github.com/hteumeuleu/switchweekly/blob/main/src/components/icon-edition.html

https://switchweekly.com/issue/290

```html
<!-- Start Icon:Edition -->
<span style="margin-right:0.2em; font-size:1.25em; display:inline-block; vertical-align:middle;" role="presentation">
	<span style="font-size:1em; display:block; border:0.1em solid; border-radius:0 0 0.1em 0.1em; pointer-events:none;">
		<span style="font-size:1em; display:block; padding:0.1em 0.1em 0.25em;">
			<span style="font-size:1em; display:table;">
				<span style="font-size:1em; display:table-cell; width:0.2em; border-bottom:0.25em solid;"></span>
				<span style="font-size:1em; display:table-cell; padding-left:0.05em;">
					<span style="font-size:1em; display:block; margin-bottom:0.05em; width:0.35em; border-bottom:0.1em solid;"></span>
					<span style="font-size:1em; display:block; width:0.35em; border-bottom:0.1em solid;"></span>
				</span>
			</span>
		</span>
	</span>
</span>
<!-- End Icon:Edition -->
```