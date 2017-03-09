---
layout: post
title: "On the Event Loop in RPGs"
date: 2017-03-08
---

In the wake of a hideously stressing phase of my life, including having reliable access to the internet in my own home - one of the lifebloods of developers of any age - stripped from me completely, I resumed playing _Etrian Odyssey IV_ (or just _EOIV_ for short), which I got myself for Christmas. My formulating these ideas predates me purchasing it, but I'll be using examples from it out of convenience.

The quick-and-dirty summary of this is that for most turn-based RPGs, and even most non-turn-based ones, the battle system can be approximated with some entity objects and an event loop.

The simplest possible examples are likely also the earliest, when the only options you had were Attack, Magic, Item, and Escape and none of the spells did anything particularly fancy. If you wanted you could even do that much with no events, although it would mean all the objects directly affecting each other in a way that wouldn't be very feasible if for some reason a webserver needed to know about it.

But even at that basic level, the event loop for a single round of combat looks something like this:

> 1. "Beginning of round" event.
> 2. For each member of the party and each enemy in the combat:
>   1. "Beginning of turn" event for that entity.
>   2. "Action" event for that entity, depending on what it does.
>   3. "End of turn" event for that entity.
> 3. "End of round" event.

Each entity would have a handler for what happens if they get attacked or have a firebolt slung at them or something like that - a designer/developer that wants to obscure their damage formulae would even be able to have the action event carry the potential damage unmodified by the target's ability to defend against it.

However, this doesn't use the power of the event loop to its full potential. In many more modern games you see things like dedicated damage-soaking characters or classes that intercept incoming attacks for vulnerable characters or reaction abilities.

For example, the Sniper class in _EOIV_ can learn a skill called "Chase Bind", which causes any enemies that receive a bind - a category of status effect not unlike Silence, although each type of bind also has effects beyond disabling certain actions - to be immediately attacked.

In order to implement something like that, somewhere in the process of an enemy being bound a "Bind" event would be fired in order for the Sniper's event handling to pick up. Whether the binding skill itself does so or the bind effect it created does so is irrelevant, but in my own implementation I'd likely have the effect fire it when it attaches to the enemy object - multiple ways to inflict each type of Bind exist.

That's a simple example comparatively, so let's look at another. One of the most basic skills the Landsknecht can learn is "Sonic Raid", which looks simple in practice - it always goes first.

There are a number of ways to handle this. Most of the games in the series use speed modifiers on skills, and the likely explanation is that Sonic Raid's speed modifier is just very high. But we have another way - what if we had it give the Landsknecht a temporary event handler when it was picked? The handler might look like this:

> this.once("round_start", sonicRaid(target))

Of course, this is just an alternative - an alternative worth considering, since what this opens up is the possibility for two actions to occur nearly simultaneously. This isn't commonly seen in most turn-based games so that the player can process what's happening, though, so more realistically there'd be some kind of tiebreaker (probably based on whatever determines the entities' turn order under normal circumstances).

More can be done with this, so it's more of an introduction than anything.
