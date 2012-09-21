shx-for-emacs
=============

shx &quot;shell-extras&quot; extends comint-mode in a couple small ways.
It displays small plots and graphics, changes how page up/down work,
and intercepts commands entered at the shell to call elisp functions.

This version has been tested with Emacs 24.0.94 on Mac OS X.

Warning!
========

This software makes EMACS react AUTOMATICALLY to the text displayed
in a buffer.  Since this text might be beyond your direct control,
EMACS will be too.  I've taken steps to make sure that shx only
triggers in a SAFE manner (see 'shx-safe-to-trigger') but shx is
inherently unsafe and comes without any warranty.


Installation
============

Either:

1. Move shx.el to a directory in your 'load-path' or alternatively
add the directory shx.el is in to your 'load-path' by adding this
line to your ~/.emacs:

 (add-to-list 'load-path "~/Downloads/elisp/")

2.Add this line to your ~/.emacs:

 (require 'shx)

If you want shx to run in any comint-mode buffer, add this line too:
 (add-hook 'comint-mode-hook 'shx-activate)

Graphical functions (like plotting) use these programs:

- convert (i.e., ImageMagick, used to scale images to size)
- gnuplot (for all plotting functions)
- wget    (for pulling image files from google maps)

The variables 'shx-convert-cmd' 'shx-gnuplot-cmd' and
'shx-wget-cmd' can be customized as needed (or edited in shx.el) to
point to these binaries.


Quick-Start
===========

1. Finish the installation (as above).
2. Type M-x shx (enter) to begin a shell session using shx
3. Type :man ls
4. Type echo -e "##done()"
5. Type :help
6. Type :test  (hopefully you see nothing but a success message)
7. Try to page up, enter a command, then page back down

Detailed help can be found in the next few sections.


shx Input Commands
==================

shx's support for input commands written in elisp give it a lot of
the same advantages as 'eshell'.

Everything you need to know about shx's input commands can be found
in the help.  Just type :help on an empty line and press enter.

These special commands are executed asynchronously of the
underlying shell process because EMACS actually intercepts them.
For example you can type ":man gcc" even while gcc is busy
compiling and a window with the gcc man page will come up in EMACS.

You can change 'shx-prefix' from ":" to "# ",

 (setq shx-prefix "# ")

in which case you would, for example, type "# help" to access the
help.

Or you can set the prefix to nothing all:

 (setq shx-prefix "")

The commands that get intercepted by shx will have the
'shx-highlights' face, whereas commands which were not intercepted
will have the default 'comint-highlight-input' face.

Many existing commands are for displaying graphics such as plots in
a shell buffer.  These require ImageMagick, wget, and gnuplot to be
installed.  Others invoke built-in EMACS functionality, like :man,
:edit, :grep, :delay.

Users can write new commands by defining a single-argument function
of the form shx-COMMAND, where COMMAND (which must be capitalized)
is what the user would type to invoke it.  For example if you put
this in your .emacs:

 (defun shx-BREAK (arg) (insert "Break!") (shx-send-break))

... a user can type :break to send a break straight through.  See
'shx-DIFF', 'shx-GREP' for examples.

If you write a new command that you think might be useful to
others, send it along to me and hopefully I can include it.


shx Command Triggers
====================

Triggers can be used to enhance command-line applications.  This is
done by having the application echo a shx command trigger on a new
line by itself:

 ##COMMAND(ARGUMENT)

For example if a program outputs the following line:

 ##view(mountains.png)

... then mountains.png will be displayed in the shell, scaled to fit.

You can control how large the image appears on-screen by customizing
the variable 'shx-imgsize', or by executing:

 (setq shx-imgsize 300)


shx Scrolling
=============

shx supports splitting of a shell window on paging up and down.
When you page up, the frame is split in two with a larger
"scrolling frame" on top and a smaller "input frame" preserved on
the bottom.  This lets you enter text at the prompt (in the input
frame) and monitor new input while consulting previous output (in
the scrolling frame) uninterrupted.

You can change the size of the input frame to something else:

 (setq shx-split-rows 15)


shx Keybinding Modifications
============================

- Recognized URLs are turned into mouse/keyboard accessible (C-c b)
  links and a history of previous links is maintained.

- C-c C-c sends C-c to the foreground process

- C-c C-k sends SIGKILL to the shell (what C-c C-c did before).

- When the prompt is a ":" (such as when reading through a man
  page), leading spaces and 'q's are sent straight to the process
  rather than being inserted into the buffer.


Priorities
==========

Testing
