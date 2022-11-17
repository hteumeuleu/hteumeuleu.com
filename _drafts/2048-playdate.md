---
title:  "I made 2048 for the Playdate"
---

Earlier this year, I got my [Playdate](https://play.date/). It’s a tiny handheld game system with a black-and-white screen and… a crank. [I fell in love](https://twitter.com/HTeuMeuLeu/status/1131417626188685312) the moment it was announced. I even made [an interactive email](https://www.hteumeuleu.com/2019/interactive-animation-in-html-email/) based on it back in 2019. One of the biggest selling point for me is the fact that anyone can make and sideload games on the device. Thinking about it, this may be the first time I own a gaming console I can (legally and easily) make games for.

I’ve been hard at work learning Lua and how to use the Playdate SDK. In august, I released my first “app” for the system: [Playorama](https://hteumeuleu.itch.io/playorama), a video player (in which you can control video playback with the crank). After that project, I really wanted three things:

1. To **make a game**.
2. To **learn more** about using sprites.
3. To **make something fast**, in a week or two, avoiding the three months of development it took me to release Playorama.

(Only two out of the three last statements did happen.)

This is how [2048](https://play2048.co/) came to my mind. The web-based game was a huge hit almost a decade ago and spun a ton of clones (despite itself being a clone of [Threes!](https://apps.apple.com/us/app/threes/id779157948) on iOS). Last month, I released my adaptation of [2048 for Playdate](https://hteumeuleu.itch.io/2048). And although I’m still very new to this, coming from web development (and more specifically HTML emails), here are **three things** that I think are pretty interesting about this project.

# Collisions

```lua
function Tile:collisionResponse(other)

    if other.mustBeRemoved or other.mustBeMerged or other:getTag() ~= self:getTag() then
        return playdate.graphics.sprite.kCollisionTypeFreeze
    else
        return playdate.graphics.sprite.kCollisionTypeOverlap
    end

end
```

# Using the crank

I really wanted this game to support the crank. So I thought it could be fun to be able to use the crank instead of the d-Pad to move tiles around. Moving the crank up would move the tiles up. Moving the crank towards you would move the tiles left. And so on.

And to be able to visualize what the crank is doing, I wanted to have a little cursor moving around the grid. This is basically about moving a sprite around on specific coordinates. And in order to do this, I used… an [Animator](https://sdk.play.date/1.12.3/#C-graphics.animator)! According to the docs, animators are “_lightweight objects that keep track of animation progress. They can animate between two numbers, two points, along a line segment, arc, or polygon_”.

So first I created a `polygon` representing the outline of the grid. And then I created an `animator` using this `polygon` and set to a duration of 360.

```lua
self.animator = playdate.graphics.animator.new(360, {polygon}, playdate.easingFunctions.linear)
```

Then, all I have to do is get the crank current absolute position and get the animator value at that angle.

```lua
local angle = playdate.getCrankPosition()
self.animator:valueAtTime(angle + 45)
```

I used a `+ 45` here because I created my polygon from the top left. But the angle of the crank starts from the top center.

# My first bug report

Although I did [a beta version](https://github.com/hteumeuleu/2048/releases/tag/v1.0.0-beta.1) and asked for feedback on the [Playdate Squad discord](https://discord.com/invite/playdatesquad), the first feedback I got after release on Itch was… embarassing:

> Hey, I got a bug. Somehow it combined a 1024 block with a 256 block and gave me a 512 block.

Oops. That’s pretty bad. One of these “*You had one job*” moment. The worst part is that, I’m fairly certain I had seen this bug early in development. But I didn’t manage to reproduce it consistently. And because I had so much going on at that time, and did so much refactoring since, I thought this bug was gone in the process.

Nope.

After a lot of testing to reproduce this consistently, I figured it out. It all came back to how I managed collisions.

```lua
if other.mustBeRemoved or other.mustBeMerged or other:getTag() ~= self:getTag() then
    return playdate.graphics.sprite.kCollisionTypeFreeze
else
    return playdate.graphics.sprite.kCollisionTypeOverlap
end
```

See that `getTag()`? There was my problem. [According to the SDK](https://sdk.play.date/1.12.3/Inside%20Playdate.html#m-graphics.sprite.setTag), a sprite’s tag is a “_an integer value useful for identifying sprites later, particularly when working with collisions_”. So for each tile, I assigned a tag equal to the tile’s value.

```lua
function Tile:init(value)

    -- …
    self:setTag(value)
    -- …

end
```

That seemed simple enough and straightforward at the time. A `256` tile would have a tag set to `256` as well. And a `1024` tile would have a tag set to `1024`. So when each tile’s tag value would be compared, there’s no way they would be equal and thus would not merge. Right?

Little did I know that the integer used for the tile is an 8-bit integer. This is [mentioned in the C SDK](https://sdk.play.date/1.12.3/Inside%20Playdate%20with%20C.html#f-sprite.setTag) but completely absent from the Lua’s one. An 8-bit integer means it can only hold values from 0 to 255. Any value superior to that would be the remainder of it divided by 256.

And now everything made sense. With my code, a `256` tile would have a tag set to `0`. And so would a `1024` tile. And thus they would merge.

The fix was simple. All I needed was to set a value to tags between 0 and 255. Since the game is based on power of twos, I could use the binary logarithm of the tile’s value. So a `256` tile now has a tag set to `8`. And a `1024` tile has a tag set to `10`.

```lua
function Tile:init(value)

    -- …
    self:setTag(log2(value))
    -- …

end
```

According to [a quick search](https://puzzling.stackexchange.com/questions/48/what-is-the-largest-tile-possible-in-2048), the largest theorical value of a tile is `131 072`, making it a tag of `17`. So there’s still enough room before hitting that 8-bit limit!

---

I hope you enjoyed reading this post as much as I enjoyed writing it. You can [download 2048 for Playdate](https://hteumeuleu.itch.io/2048) on Itch right now.