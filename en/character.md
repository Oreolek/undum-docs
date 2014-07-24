# `Character`

The character is created for you, but is passed into most of the
functions that you define. It consists of an object with no methods
and two properties:

#### `qualities`

The qualities object maps quality identifiers to their current
value. Your code finds the current value associated with a quality by
reading from this object, for example:

    var gold = character.qualities.gold;

To set a quality, you have two choices. If you know the quality you
want to set will not appear in the user interface, then you can set it
directly:

    character.qualities.gold += 1;

If it does appear on-screen, then this approach will mean that the
character panel doesn't update, and the screen will be out of sync
with the actual value. Instead it is better to use the `System` method
`setQuality`, which also updates the UI:

    system.setQuality('gold', character.qualities.gold+1);

It is fine to use `setQuality` if the quality isn't visible, making
this the preferred option for all quality modifications.

#### `sandbox`

Not every bit of data you want to associate with a character fits
nicely into a quality. The sandbox is a general storage space for any
further data you want to store in the course of your game, for
example:

    character.sandbox.roomsSearched.push('bathroom');

Sandbox data is never visible to the user, so you can use any data
structures you like here, to any depth.
