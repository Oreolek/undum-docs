# `System`

The system represents the interface between your code and the
user-interface.  You don't create your own `System` object, it is
passed into your code.

#### `clearContent(elementSelector)`

*Since version 2*

Removes all content from the page, clearing the main content area.

Although Undum is designed to keep the flow of the narrative on one
page, sometimes you do need to start a new page, and this allows you
to do so.

The `elementSelector` is options. If you give it, then the DOM element
matching the selector is cleared, rather than the whole document.

#### `write(content, elementSelector)`

Writes new content to the main flow of the story. The content you pass
in must be either valid DOM elements already, or else be a string
containing text in Display Content format.

The `elementSelector` is optional. If you provide it, then the new
content will be added after the DOM element in the document that
matches the selector. This allows you to do out-of-order addition of
content. Simply add a paragraph with an id in your game, then later
you can give this id as a selector to write, and the new content will
be inserted immediately following that paragraph, regardless of how
much extra content has been added since that point. If no selector is
given then `#content` is used, i.e. the content is added at the end of
the document. The `writeBefore` method inserts content at the start of
the document, or before a selector.

The story will scroll to the start of the insertion point. If you do
not wish to animate this scrolling, but just jump right there, you can
switch off jQuery's animation system by adding `jQuery.fx.off=true` to
your initialization code. This is particularly useful when debugging.

#### `writeHeading(content, elementSelector)`

Writes new content into the story and formats it as a heading. This
method work exactly as `write`, but wraps the content you provide into
a `h1` html tag.

#### `writeBefore(content, elementSelector)`

Writes content into the story. This method is identical to `write`,
above, except that the content is written at the start of the story,
or if a selector is given, inserted before the matching element. On
browsers that support it, the story will be scrolled to the insertion
point.

#### `writeChoices(listOfSituationIds)`

*Since version 2*

Creates a standard block of choices, one for each of the given
situation ids. The text used in the links will be whatever is returned
by the situation's `optionText` method. In addition, if the
situation's `canChoose` method returns `false`, then the option will
be displayed, but will not be clickable.

#### `getSituationIdChoices(listOfIdsOrTags, minChoices, maxChoices)`

*Since version 2*

This function is a complex and powerful way of compiling implicit
situation choices. You give it a list of situation ids and situation
tags. Tags should be prefixed with a hash # to differentiate them from
situation ids. The function then considers all matching situations in
descending priority order, calling their canView functions and
filtering out any that should not be shown, given the current
state. Without additional parameters the function returns a list of
the situation ids at the highest level of priority that has any valid
results. So, for example, if a tag #places matches three situations,
one with priority 2, and two with priority 3, and all of them can be
viewed in the current context, then only the two with priority 3 will
be returned. This allows you to have high-priority situations that
trump any lower situations when they are valid, such as situations
that force the player to go to one destination if the player is out of
money, for example.

If a `minChoices` value is given, then the function will attempt to
return at least that many results. If not enough results are available
at the highest priority, then lower priorities will be considered in
turn, until enough situations are found. In the example above, if we
had a minChoices of three, then all three situations would be
returned, even though they have different priorities. If you need to
return all valid situations, regardless of their priorities, set
minChoices to a large number, such as `Number.MAX_VALUE`, and leave
maxChoices undefined.

If a `maxChoices` value is given, then the function will not return any
more than the given number of results. If there are more than this
number of results possible, then the highest priority resuls will be
guaranteed to be returned, but the lowest priority group will have to
fight it out for the remaining places. In this case, a random sample
is chosen, taking into account the frequency of each situation. So a
situation with a frequency of 100 will be chosen 100 times more often
than a situation with a frequency of 1, if there is one space
available. Often these frequencies have to be taken as a guideline,
and the actual probabilities will only be approximate. Consider three
situations with frequencies of 1, 1, 100, competing for two
spaces. The 100-frequency situation will be chosen almost every time,
but for the other space, one of the 1-frequency situations must be
chosen. So the actual probabilities will be roughly 50%, 50%,
100%. When selecting more than one result, frequencies can only be a
guide.

Before this function returns its result, it sorts the situations in
increasing order of their `displayOrder` properties.


#### `doLink(URL)`

Carries out the action associated with the given URL, as if it had
been the `href` of a HTML link that the user clicked. This allows you
to procedurally change situation and carry out actions from your code.

#### `rnd`

This holds a general purpose random number generator. It is an object
derived from the `Random` prototype, so see `Random` below for details
on its API.

#### `time`

This is a numeric value holding the current time, in seconds, since
the player began playing the game. This value is correctly propagated
across saves, so it is the only way you should track timing. In
particular you should never call `new Date()` and use that value to
determine the outcome of any event. You can use the current date to
display the current date, for example, but not to control what actions
or situations are available. See the section on Loading and Saving for
more details of why this is important.

#### `setQuality(qualityId, newValue)`

Sets the character's given quality to the given value. This function
also updates the character panel, animating the change in value if
that is necessary. Do not directly set quality values in the
character, because the user-interface will not detect and reflect
those changes.

#### `animateQuality(qualityId, newValue, options)`

Like `setQuality`, this function changes the current value of the
given quality. In addition, however, it displays a progress bar that
shows to the user how the value has changed. The `options` parameter
should be an object containing options for how the bar should
display. The available options are:

- `from`: The proportion along the progress bar where the animation
   starts. Defaults to 0, valid range is 0-1.

- `to`: The proportion along the progress bar where the animation
   ends. Defaults to 1, valid range is 0-1.

- `showValue`: If `true` (the default) then the new value of the
   quality is displayed above the progress bar.

- `displayValue`: If this is given, and `showValue` is `true`, then
   the given value is used above the progress bar. If this isn't
   given, and `showValue` is `true`, then the display value will be
   calculated from the `QualityDefinition`, as normal. This option is
   useful for qualities that don't have a definition, because they
   don't normally appear in the UI.

- `title`: The title of the progress bar. If this is not given, then
   the title of the quality is used. As for `displayValue` this is
   primarily used when the progress bar doesn't have a
   `QualityDefinition`, and therefore doesn't have a title.

- `leftLabel`, `rightLabel`: Underneath the progress bar you can place
   two labels at the left and right extent of the track. These can
   help to give scale to the bar. So if the bar signifies going from
   10.2 to 10.5, you might label the left and right extents with "10"
   and "11" respectively and have the `from` and `to` value be 0.2 and
   0.5 respectively. If these are not given, then the labels will be
   omitted.

#### `setCharacterText(content)`

Sets the block of character text that appears in the character
panel. As for the `write` method, this text should be either valid DOM
elements, or a string meeting the Display Content requirements.

#### `clearLinks(URL)`

Call this function with an Undum link URL (e.g. `ballroom`, or
`ballroom/open-cabinet`). It will remove all occurrences of that link
from the page. This is equivalent to what happens when you change
situation, or when you click a link marked with the `once` CSS
class. It allows you to control what options are available
dynamically, from your code.

## `Random`

The `Random` object provides a set of tools for simple random number
generation, in a way that is guaranteed to work with the Loading and
Saving functionality in Undum. An instance of `Random` is provided in
the `rnd` property of the `System` object, so you will never need to
create your own. It has the following methods:

#### `random()`

Generates a random number between 0 and 1, where 0 is inclusive, and 1
is exclusive. You can use this to check against known probabilities,
such as:

    if (system.rnd.random() > 0.5) {
        ...
    }

To check for a 50/50 chance.

#### `randomInt(min, max)`

Return a random number between the given two values, where both values
are inclusive. So `randomInt(2,3)` generates either 2 or 3.

#### `dice(n, dx, plus)`

Rolls _n_ dice with _dx_ sides and adds _plus_ to the result. This
allows you to easily get results for rolls from regular RPG-style
games, such as 3d6+2. The `plus` parameter may be negative or
positive.

#### `aveDice(n, plus)`

Rolls _n_ averaging dice, and adds _plus_ to the result. Averaging dice
are a special type of d6 with sides marked [2,3,3,4,4,5]. They represent
the fact that most people are fairly average, and results should not lie
at the extremes.

#### `diceString(definition)`

Rolls dice according to the given definition string. The string should
be of the form xdy+z, where the x component and z component are
optional. This rolls x dice of with y sides, and adds z to the result,
the z component can also be negative: xdy-z. The y component can be
either a number of sides, or can be the special values 'F', for a
fudge die (with 3 sides, +,0,-), '%' for a 100 sided die, or 'A' for
an averaging die (with sides 2,3,3,4,4,5).
