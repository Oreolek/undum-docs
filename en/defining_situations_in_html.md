# Defining Situations in HTML

*New in Version 2*

From v.2, Undum supports defining situations entirely from HTML. The
tutorial game files show this in action, if you want to see a concrete
example.

### Required Data

Situations defined in HTML should be diven as a `div` with
`class="situation"` set. The content of the `div` will be turned into
a new `SimpleSituation` (see the <a href="./API.html">API
document</a>), with the content of the div output when the situation's
`enter` method is called. In addition to the `class="situation"` and
the HTML content, the `div` also requires its `id` attribute to be
set, and the value of this id will be used as the situation id for the
newly created situation. Situations created from HTML are instances of
the `SimpleSituation` prototype.

The game scans the HTML file for situations, compiles them and adds
them to the main `game.situation` dictionary. Because the situations
end up in the same place, you can freely mix the two methods, defining
some situations in HTML, and others in Javascript. For this reason,
you can't have a situation defined in the HTML with the same id as one
in your Javascript file. This will raise an error.

### Optional Data

In addition to the id and content of the situation, additional options
can be set by defining one or more of the following `data-` attributes
on the `div`. Each is interpreted and passed to a corresponding option
on a `SimpleSituation` object, so to find more information on what
each value means, see the <a href="./API.html">Javascript API</a>.

#### data-option-text

If defined, this specifies the label that will be used when the
situation appears in an choice block. In Javascript, this can be
either fixed text, or a function. In the HTML API, only fixed text is
supported.

#### data-can-view

If defined, this should be the body of a function that will be
compiled and attached to the situation's canView method. The
content of this attribute will be wrapped with:

    (function(character, system, situation) {
    ... HTML attribute ...
    })

to make the final function. So, your definition should end with a
`return` statement. For example:

    <div id="situation-id-1" class="situation"
     data-can-view="return character.qualities.foo == 2">

This is intended to be similar to the way browsers function with event
handlers on HTML tags, such as `onClick`. The actual semantics are
difficult to replicate exactly, and there may be use-cases that are
impossible, but for most uses, this will do the right thing.

#### data-can-choose

If defined, this should be the body of a function that will be
compiled and attached to the situations canChoose method. See the
description of `canView`, above, for details of how the value in this
attribute is interpreted.

#### data-priority

If given, this is parsed as a number and set as the situation's
`priority` value.

#### data-frequency

If given, this is parsed as a number and set as the situation's
`frequency` value.

#### data-display-order

If given, this is parsed as a number and set as the situation's
`displayOrder` value.

#### data-tags

If given this should be a set of tags to label the situation with. The
string is split at commas, spaces and tabs. So "foo, bar cube
dock-trog" is parsed as four tags: "foo", "bar", "cube" and
"dock-trog".

#### data-heading

If given, then this heading will be used as the `heading` property for
the SimpleSituation. If no `data-option-text` is given, the heading
will also be used in choice blocks.

#### data-choices

If given, this should be a list of items to use in generating an
implicit set of situation choices, exactly as for the `choices`
property of `SimpleSituation`. The values of this list is parsed
exactly as for `data-tags`, above, splitting at commas, spaces and
tags.

#### data-min-choices

If `data-choices` is given, this value can also be specified. If so,
it is interpreted as an integer and passed to the `minChoices` option
of `SimpleSituation`.


#### data-max-choices

If `data-choices` is given, this value can also be specified. If so,
it is interpreted as an integer and passed to the `maxChoices` option
of `SimpleSituation`.
