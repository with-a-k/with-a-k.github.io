---
layout: post
title: "On Complicated State in React"
date: 2017-07-14
---

As I thought, getting Skills off the ground was not easy. Not as hard as I was imagining it to be, but still not easy.

A character can have zero or more Skills, but the character object in the builder needs to know all of the following things for each of them:
 - It needs to know the skill's _name_, or rather its value in the selector, for multiple reasons: most Skills can't be taken multiple times, and some Skills require others to take.
 - It needs to know the skill's _level_ so that it can be shown in the skill view.
 - It needs to know _how much SP it costs, total_, so that it can prevent the user from going over, or prevent them from taking too many Flaw-class skills.
 - It needs to know _any passive bonuses it awards_ so they can be reflected in the displayed attributes. (The exception is a skill called "Balanced Fighter", which provides conditional bonuses; its bonus is thus "true".)
 - It needs to know _an internal, per-character ID number_ so that React can keep track of it across sorts. There are tons of other posts about this kind of thing, which is usually referred to as "keying".

Skills obviously have other things to keep track of, such as their description and level speed, but these things aren't needed outside the skill itself. These are in the Skill Constants file, and are in the process of being added.

Then given the fairly large number of ancestors between "individual Skill view" and "character state container", wiring up "this thing has changed" handling to alter the state accordingly was a bit of a challenge.

I'll need to apply the same logic on a larger scale when Skills are finished and it's time to move on to Techniques.
