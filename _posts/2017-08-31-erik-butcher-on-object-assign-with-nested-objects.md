---
layout: post
title: "On Object.assign() With Nested Objects"
date: 2017-08-31
---

This is also another post related to Valor Builder; there was another issue I encountered that it turned out arose because I was using something improperly.

`Object.assign(target, ...sources)` is a built-in JavaScript function that copies properties from one or more source objects to a target object. It has some rules:
 - If both the target and a source have a property, the value of that property in the target is overwritten by the value in the source.
 - If the target does not have a property, but a source does, it is declared on the target with the value from the source.
 - If the target has a property that is not found in any source, it stays what it is.
 - If multiple sources exist, later sources override earlier sources; the source objects themselves don't change but previous changes to the target are.

 With that in mind, here's a now-revised segment of the `TechniqueContainer` code. This function is responsible for changing a Technique's core power, based on an event from an `<input type="number">`:

 ```javascript
 changePower(event) {
   const newPower = parseInt(event.target.value, 10);
   if (!this.powerIsValid(newPower)) return;
   let data = Object.assign(this.props.technique,
      {
        core: {
          power: newPower
        },
        level: this.calculateLevel(newPower),
      });
   this.update(data);
 }
 ```

What this does:
 1. Turn the value from the input into a number. Regardless of type, HTML input elements store string values, which has resulted in at least one instance of having numbers that are way too big (or small) for their context.
 2. Check whether the new power is valid. Return nothing if it isn't.
 3. Create a data package by Object.assigning properties onto this.props.technique.
 4. Call the "update" prop, which moves the data up the component hierarchy (to CharacterBuilder, i.e. the datastore/central source of truth.)

If you've found the problem already, good job.

The issue here is that the `core` property of this.props.technique is being completely overwritten by a new object - Object.assign() isn't recursive or anything like that. The CharacterBuilder would get a "hey, update this Technique" but there wouldn't be a core name in it. There's conditional rendering based on what core a Technique is, so it was a major problem.

But it got me thinking about making a version that _did_ work the way I thought...
