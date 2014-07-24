# Creating an Undum Game

## File Structure

An Undum game consists of a single HTML file. This imports the game
engine and any supporting files. It also provides the structure needed
for Undum to place content correctly on-screen.

The Undum engine consists of a single Javascript file, `undum.js`. In
order to work, however, it needs supporting files. It requires the
jQuery library (http://jquery.com) to be already imported, and it
requires a game definition file to be imported after it.

The normal pattern of imports in the HTML file is therefore:

    <script type="text/javascript" src="media/js/jquery-1.4.2.min.js"></script>
    <script type="text/javascript" src="media/js/undum.js"></script>
    <script type="text/javascript" src="media/js/mygame.game.js"></script>

By convention, the Javascript files are held in a `media/js`
directory, but this is not enforced, you can arrange these files as
you like, as long as you update the references to match.

In addition to the Javascript files, Undum expects the HTML file that
imports it to have a particular structure. Although there is a good
deal of flexibility, if you need it, you should start with the HTML
file that is provided.

Finally, the HTML file will include a CSS file that controls the look
and feel of the page. There are some elements in the CSS file which
are used by Undum, and so, as for the HTML page, you should start with
the CSS files provided.  In most cases you will be able to leave the
CSS file untouched, or else just tweak colors and image imports to
match your game's style.

## The HTML File

The sample HTML file provided shows the key points to edit. They are:

* The game's title.

* The top-left panel title and summary (you can leave this as an
  explanation of Undum, or change it to be more game-specific).

* The copyright message. Please note that there is also a message
  that acknowledges your game is based on Undum. You can remove this
  entirely, but if you do leave it in place, that helps people
  find the software and perhaps write their own game.

* The path to your game definition file. By convention, game
  definition files are named `yourgamename.game.js`.

In addition, from v.2 onwards, Undum supports defining Situations
entirely within HTML. These situations are scanned and added to
Situations defined in your Javascript Game Definition file, allowing
you to have the best of both worlds. HTML Situations are discussed in
the page on the <a href="./HTML.html">HTML API</a>.


