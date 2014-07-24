# `Situation`

The `Situation` object is the prototype of all the situations in your
game. It can be used directly, or through its more common derived
type, `SimpleSituation`. The base `Situation` gives you maximum
flexibility, but `SimpleSituation` provides more functionality and can
produce terser code.

#### `new Situation(options)`

Creates a new situation. The options array can specify your
implementation for any or all of the following methods of this class:

- `enter`
- `exit`
- `act`
- `optionText`
- `canView`
- `canChoose`

(see below for explanations of those methods). This allows
you to easily create situations that override certain behaviors with
code such as:

    Situation({
        enter: function(character, system, from) {
            ... your implementation ...
        }
    });

without having to subclass `Situation` to provide your own
implementations.

In addition the following options can be passed in.

- `tags`: A list of tags with which to label this situation. These are
primarily used to generate implicit lists of choices with
`System.getSituationIdChoices`. Tags are arbitrary strings. You can
pass a list of strings, or a single string. If you pass a single
string, it will be split at spaces, commas and tabs to form a list of
tags. For this reason, tags normally do not contain spaces, commas or
tabs (though if you pass in a list, and don't expect Undum to do the
splitting for you, you can include any characters you like in a tag).

- `optionText` (as a string): If given as a string, rather than a
function, this text will be returned whenever `optionText(...)` is
called.

- `displayOrder`: A numeric value, defaults to 1. When displaying
lists of implicitly generated choices, the options be displayed in
increasing value of this parameter.

- `priority`: Can be any number, defaults to 1. When generating lists
of choices implicitly, situations are considered in descending
priority order. If higher priority situations can be displayed, lower
priority situations will be hidden. See `System.getSituationIdChoices`
for details of the algorithm.

- `frequency`: Any number, defaults to 1. When generating lists of
implicit choices, where there are more choices available that slots to
display them, situations will be chosen randomly, with a probability
based on this value. See `System.getSituationIdChoices` for details of
the algorithm.

#### `enter(character, system, from)`

This is called when Undum enters a situation. The `character` and
`system` are instances of `Character` and `System` as described
above. The `from` parameter is a string containing the situation
identifier for the situation that we're arriving from.

This method is the most commonly overridden. It is commonly used to
describe the current situation (by sending content to
`system.write()`) and to update the character (by calling
`system.setQuality()` or by changing data in the character's `sandbox`
object)..

#### `exit(character, system, to)`

This method takes the same `character` and `system` parameters as
`enter`. Its third parameter, `to`, is a string containing the
identifier of the situation we're exiting to.

#### `act(character, system, action)`

This method again takes the same `character` and `system` parameters
as before. Its third parameter is a string containing the action
identifier corresponding to the link the player clicked. It is common
to use an `if` statement or a `switch` to query this action identifier
and decide what to do accordingly. For situations in which many
different actions are possible, consider using the `SimpleSituation`
prototype, which provides this switching behavior for you.

#### `optionText(character, system, hostSituation)`

This method is called by `System.writeChoices` to generate a label for
links to this situation. The `hostSituation` is the situation that has
asked for the choices to be displayed.

#### `canView(character, system, hostSituation)`

This method is called by `System.getSituationIdChoices` to determine
whether this situation can be part of a list of choices in the current
game state. It should return true or false.

#### `canChoose(character, system, hostSituation)`

This method is called by `System.writeChoices` to determine whether a
link should be added to allow the user to enter this situation. If
not, the choice will still appear, but will not be clickable.



## SimpleSituation

This prototype builds on the basic `Situation`, providing tools to
make it easy to output content in the `enter` method, and to switch
between different functions depending on the action identifier passed
into the `act` method. The `exit` method of `SimpleSituation` is
exactly as for the base type `Situation`.

#### `new SimpleSituation(content, options)`

Creates a new simple situation that will display the given content
when its `enter` method is called. The given options dictionary
provides further control of the behavior of this type. Valid options
are:

- `enter`: Providing an enter function in the `options` parameter
   allows you to add additional behavior to the enter method. Your
   custom function will be called *in addition to* and *after* the
   default content is written to the screen. You cannot override
   `SimpleSituation`'s `enter` method by providing this function. To
   override the method, you would have to create a derived type. If
   you provide an `enter` function, it should have the same form as
   `Situation.enter`.

- `act`: Pass in a function to add additional behavior to the act
   method. As for `enter`, your method is called *in addition to* and
   *after* the built-in functionality.

- `exit`: Because `SimpleSituation` has no default behavior for
   `exit`, any function you pass in here will be the only exit
   behavior for the object you are creating.

- `heading`: The `content` that you specify will be written out
   verbatim.  You can include headings in this content. Often it is
   more convenient to pass in just the text in the `content`
   parameter. In that case you may define this `heading` parameter to
   display a heading before the text. Unlike `content`, this doesn't
   need to conform to the Display Content requirements.

- `actions`: This should be an object that maps action identifiers to
   responses. A response should be either some Display Content to
   write to the screen, or a function that will process that
   request. These functions should have the same signature as the
   `Situation.act` method. Each function will only be called if the
   situation receives a call to `act` with its corresponding
   identifier. This allows you to simply define functions that only
   get called when particular actions happen.

- `choices`: An optional list of tags and situation-ids, with tags
  prefixed by a has symbol to distinguish them from situation ids. If
  given, this will cause the SimpleSituation to output an implicit block
  of choices after the content.

- `minChoices`: If you have given a `choices` definition, you can set
  this to an integer value to change the number of choices that will
  appear. See `System.getSituationIdChoices` for more information on
  how this affects the output.

- `maxChoices`: If you have given a `choices` definition, you can set
  this to an integer value to change the number of choices that will
  appear. See `System.getSituationIdChoices` for more information on
  how this affects the output.

An example `SimpleSituation` definition might be:

    new SimpleSituation(
        "<p>...content...</p>",
        {
            heading: "Title",
            actions: {
                listen: function(character, system, action) {
                    if (character.qualities.hearing > 5) {
                        system.write("<p>You hear a tinkling inside.</p>");
                    } else {
                        system.write("<p>You hear nothing.</p>");
                    }
                },
                search: "<p>You find nothing.</p>"
            }
        }
    );

notice how the `listen` function is responsible for its own output,
where the `search` property is a string in Display Content format,
ready for output.

#### Functions in `SimpleSituation`

Both the `content` and the `heading` of a simple situation can be
provided either as plain text, or as a function. If you provide a
function, then it will be called with no arguments, and it should
return a string to use for the output. This enables `SimpleSituation`
to be used with other formatting and templating systems.

## QualityDefinition

Quality definitions tell Undum how and where to display a quality in
the character panel. Each quality definition has one method, `format`,
which is responsible for converting a numeric quality value into a
displayable quantity.

You define your qualities in your `undum.game.qualities` property.


#### `new QualityDefinition(title, options)`

Creates a new `QualityDefinition`. It is rare to call this constructor
yourself, most often one of the derived types of `QualityDefinition`
are used. They are defined below.

The `title` should be a string, and can contain HTML. It is used to
label the quality in the character panel. It can be any string, it
doesn't have to be in Display Content format.

Options are passed in in the `options` parameter. The following
options are available.

- `priority`: A string used to sort qualities within their
   groups. When the system displays a list of qualities they will be
   sorted by this string. If you don't give a priority, then the title
   will be used, so you'll get alphabetic order. Normally you either
   don't give a priority, or else use a priority string containing
   0-padded numbers (e.g. "00001").

- `group`: The identifier of a group in which to display this
   parameter. If a group is given, then it must be defined in your
   `undum.game.qualityGroups` property.

- `extraClasses`: These classes will be attached to the `<div>` tag
   that surrounds the quality when it is displayed. A common use for
   this is to add icons representing the quality. In your CSS define a
   class for each icon, then pass those classes into the appropriate
   quality definitions.

#### `format(character, value)`

This is called by Undum to get a human readable string representing
the given quality value for the given character. The method may return
an empty string if the value has no need to be displayed, or it may
return `null` if the quantity itself shouldn't be displayed. The
difference here is significant. If your `QualityDefinition` returns
the empty string, then the quality will appear in the character panel,
but it will have no value marked. If it returns `null`, then it will
be removed from the character panel entirely.

Most commonly the `character` parameter is ignored, but in your own
derived types you can take advantage of being able to access other
information about the character.

You may call this function yourself, but there is commonly no need. It
will be called by Undum any time the corresponding quality needs to be
displayed.

### `IntegerQuality`

This is a derived type of `QualityDefinition` that displays the
quality value by rounding it down to the nearest integer. This is
ideal for most numeric statistics.

### `NonZeroIntegerQuality`

This is a derived type of `IntegerQuality` that only displays its
value when it is non-zero. If it is non-zero then it formats in the
same way as `IntegerQuality`. Whereas `IntegerQuality` whould show
zero values as '0', this type of quality displays nothing.

### `NumericQuality`

This is a derived type of `QualityDefinition` that displays the
quality value directly, as a full floating point value.

### `WordScaleQuality`

Sometimes you want qualities displayed in words rather than
numbers. This is a derived type of `QualityDefinition` that allows you
to define words corresponding to possible quality values.

#### `new WordScaleQuality(title, words, options)`

The `title` parameter is exactly as for `QualityDefinition`.

The `words` parameter determines what words will be used. It should be
an array of strings. By default the first string will be used to
represent a value of zero (after rounding down), and the second string
a value of 1, and so on to the end of the array. Values outside the
array are treated differently depending on the value of `useBonuses`
in the `options` parameter.

The `options` parameter supports the same three options as
`QualityDefinition`. It also takes the following additional
parameters:

- `offset`: With offset=0 (the default), a quantity value of 0 will
   map to the first word, and so on. If offset is non-zero then the
   value given will correspond to the first word in the list. So if
   offset=4, then the first word in the list will be used for value=4,
   the second for value=5. You can specify a non-integer offset value,
   in this case the offset is applied *before* the value is rounded
   down.

- `useBonuses`: If this is true (the default), then values outside the
   range of words will be constructed from the word and a numeric
   bonus. So with offset=0 and five words, the last of which is
   'amazing', a score of six would give 'amazing+1'.  if this is
   false, then the bonus would be omitted, so anything beyond
   'amazing' is still 'amazing'.

### `FudgeAdjectivesQuality`

This is a derived type of `WordScaleQuality` that doesn't require you
to specify the words you wish to use. It uses the word scale from the
Fudge RPG: "terrible", "poor", "mediocre", "fair", "good", "great" and
"superb".

#### `new FudgeAdjectivesQuality(title, options)`

The parameters `title` and `options` are as for the `WordScaleQuality`
constructor. The `offset` option defaults to -3, however (in
`WordScaleQuality` it defaults to 0), making "fair" the display value
for 0.

### `OnOffQuality`

An `OnOffQuality` returns `null` from its `format` method
(i.e. removes itself from the character panel) when the corresponding
quality value is zero. Otherwise it returns the empty string (i.e. it
is shown in the panel, but doesn't have a value label). See
`QualityDisplay.format` above for more details on this distinction.

#### `new OnOffQuality(title, options)`

The constructor for this type is the same as for `QualityDefinition`
from which it is derived. It accepts one extra option:

- `onDisplay`: If given, then rather than displaying the empty string,
   it displays the given string when its corresponding value is
   non-zero. This can be used to display a check-mark, for example
   (`{onDisplay:"&#10003;"}`), or even a HTML `img` tag.

### YesNoQuality

A `YesNoQuality` displays one of two strings depending whether its
value is zero or not.

#### `new YesNoQuality(title, options)`

The constructor for this type is the same as for `QualityDefinition`
from which it is derived. It accepts two extra options:

- `yesDisplay`, `noDisplay`: Either or both of these may be given. If
   they are given, then they should be set to a string, which will be
   used to indicate non-zero or zero values, respectively. By default
   "yes" and "no" are used.

## `QualityGroup`

A quality group defines a set of qualities that should be displayed
together in the character panel, under an optional subheading. You
could use quality groups to distinguish between qualities representing
a character's innate abilities and their equipment, for example.

You define your quality groups in your `undum.game.qualityGroups`
property.


#### `new QualityGroup(title, options)`

Constructs a new quality group that will have the given `title` for a
subheading. The title may be `null`, indicating that this group does
not need a heading.

The `options` parameter should be an object with the given optional
parameters:

- `priority`: A string used to sort quality groups. When the system
   displays more than one quality group, they will be sorted by this
   string. If you don't give a priority, then the title will be used,
   so you'll get alphabetic order. Normally you either don't give a
   priority, or else use a priority string containing 0-padded numbers
   (e.g. "00001").

- `extraClasses`: These classes will be attached to the `<div>` tag
   that surrounds the entire quality group when it is displayed. You
   can use this in addition to your CSS to radically change the way
   certain qualities are displayed.
