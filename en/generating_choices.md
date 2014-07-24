# Generating Choices
## Generating Choice HTML

Thew new `System.writeChoices` method allows you to give a list of
situation id's and have Undum generate a standard looking block of
choices.

It does this by asking each choice how it prefers to be displayed, by
calling its `optionText` method. This allows situtions to change how
they are displayed depending on the current state of the character.

Also called is the situations `canChoose` method. This will normally
return true, but if it returns false, the option will still be
displayed, but not as a link, and will not be clickable. This allows
you to show the player that an option would be available, if they did
something else first, such as increase a quality.

On its own `System.writeChoices` is still mostly manual: it finds the
link text for you, and builds the HTML, but you still have to give it
a set of situation ids that you want for your choices.

## Generating Choices

Undum now also provides the `System.getSituationIdChoices` method which automatically compiles a list of situation ids, which can then be passed to `System.writeChoices` for display.
This method is powerful and complex, so we'll explore its use in increasing depth.

## Generating Choices by Tags

Situations now can have one or more tags associated with them. You can
ask `System.getSituationIdChoices` to return the ids of any situtions
that match a tag. This allows you to easily build decisions that you
can extend later. You might have a 'chapter' tag, and you mark each
situation which begins a chapter using this tag, you can then do.

    system.getSituationIdChoices(['#chapter'])

to return all chapter choices.

The way tags are processed tries to be intelligent. You can match on
more than one tag, and any situation matching either tag will be
returned, but each situation will be returned no more than once. You
can even mix tags and explicit situation ids:

    system.getSituationIdChoices(['#chapter', 'introduction', '#endmatter'])

When you only need to pass one tag to `System.getSituationIdChoices`
you can do so without using a list, so the first example above could
be equally written:

    system.getSituationIdChoices('#chapter')
