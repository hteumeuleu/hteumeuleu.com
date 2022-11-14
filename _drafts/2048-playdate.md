---
title:  "I made 2048 for the Playdate"
---

Earlier this year, I got my [Playdate](https://play.date/). It’s a tiny handheld game system with a black-and-white screen and… a crank. [I fell in love](https://twitter.com/HTeuMeuLeu/status/1131417626188685312) the moment it was announced. I even made [an interactive email](https://www.hteumeuleu.com/2019/interactive-animation-in-html-email/) based on it back in 2019. One of the biggest selling point for me is the fact that anyone can make and sideload games on the device. Thinking about it, this may be the first time I own a gaming console I can (legally and easily) make games for.

I’ve been hard at work learning Lua and how to use the Playdate SDK. In august, I released my first “app” for the system: [Playorama](https://hteumeuleu.itch.io/playorama), a video player (in which you can control video playback with the crank). For my next project, I really wanted to **make a game**. And I wanted to **learn more** about using sprites on the system. And I also wanted to **make something fast**, in a week or two, avoiding the three months of development it took me to release Playorama.

(Only two out of the three last statements did happen.)

And I thought about [2048](https://play2048.co/). The web-based game was a huge hit almost a decade ago and spun a ton of clones (despite itself being a clone of [Threes!](https://apps.apple.com/us/app/threes/id779157948) on iOS). Last month, I released my adaptation of [2048 for Playdate](https://hteumeuleu.itch.io/2048). And although I’m still very new to this, coming from web (and more specifically HTML emails) development, here are **three things** that I think are pretty interesting about my port.

# Collisions

```
function Tile:collisionResponse(other)

    if other.mustBeRemoved or other.mustBeMerged or other:getTag() ~= self:getTag() then
        return playdate.graphics.sprite.kCollisionTypeFreeze
    else
        return playdate.graphics.sprite.kCollisionTypeOverlap
    end

end
```

# Using the crank

Using an animator

# My first bug report

Although I did [a beta version](https://github.com/hteumeuleu/2048/releases/tag/v1.0.0-beta.1) and asked for feedback on the [Playdate Squad discord](https://discord.com/invite/playdatesquad), the first feedback I got on Itch was… embarassing:

> Hey, I got a bug. Somehow it combined a 1024 block with a 256 block and gave me a 512 block.

Oops. That’s pretty bad. One of these “*You had one job*” moment. The worst part is that, I had seend this bug happen early in development. But I didn’t manage to reproduce it consistently. And because I had so much going on and other bugs at that time, and did so much refactoring since, I thought this bug was gone in the process. Nope.

