Yu Engine for OpenArena
=======================

Engine forked from ioquake3 for use with OpenArena.  By default it will connect
use the OpenArena master server and use OpenArenas protocol number, removing
the need for a wrapper script like as is required with ioquake3.

Build instructions
------------------

See [the README of ioquake3](./ioq3-readme.md) for instructions on how to
compile and install this software.  Note that code for basegame, missionpack
and auto-updater has been completely removed from the source tree.  So Ignore
all documentation related to building those.

Feature list
------------

New features implemented in Yu Engine are the following:

- New cvar `cl_scaleSensWithFov` to automatically adjust mouse sensitivity as
  FOV changes to preserve the same mouse feel
- New command `reply` to reply to the most recent private message
- New command `cmdmode` (like `messagemode`) for quickly entering console
  commands
- New command `replymode` (like `messagemode`) for quickly entering a reply
  message
- Override default font with internal one using cvar `r_useInternalFont`
- Randomized GUID using cvar `cl_randomguid`
- Hor+ FOV
- Additional keyboard editing shortcuts for editing text fields
- Improved line-editing in TTY console
- Tabbed console with filtered messages

Cvar `cl_scaleSensWithFov`
--------------------------

This cvar was added because the gamecode used in Quake3 and OpenArena
calculates the zoom sensitivity with an incorrect formula.  This can cause aim
to be a bit inconsistent if you aren't used to it already.

When this cvar is set to non-zero.  The engine will automatically adjust mouse
sensitivity according to the current FOV to preserve the same feel.  This not
only affects the sensitivity when zooming but also when changing `cg_fov`.  The
exact value of the `sensitivity` cvar is used at 90 degree FOV.  Then it
increases or decreases as the FOV changes.

Default value for `cl_scaleSensWithFov` is `0`.

Cvar `r_useInternalFont`
---------------------------------

The default OA font used for the console and some in-game text doesn't look
very good.

It is possible to include an custom made internal font, which looks better (in
my opinion).  This is controlled by the Makefile variable `BUILD_INTERNAL_FONT`
(it is set to `1` by default) when building the renderer libs.  When the
renderer has been built using this option, there is a cvar called
`r_useInternalFont` which when set to a non-zero value will override the
default font with the internal one.   The default value for `r_useInternalFont`
is `0`.

When building the internal font, each renderer gets a copy of the font.  This
adds ~100KB of memory to each of the renderer libs.  This isn't very much
considering that the opengl1 renderer is over 3.5 MB without the included font.
But if you don't want to use this feature, you can disable the build option and
save some memory.

Cvar `cl_randomguid`
--------------------

In OpenArena each player have a special value called "guid" used for
identification.   It is a 128-bit number printed in hexadecimal and can be is
available through the readonly cvar `cl_guid`.  The number is usually generated
by creating an md5 hash from the special file called `qkey` in your
`fs_homepath` folder.  This is used for server admins to identify a specific
player, which is useful when a specific player should have access to special
commands on the server.

Some people want to play completely anonymously, and then GUID will reveal
their identity.  By setting the cvar `cl_randomguid` to a non-zero value, a new
GUID will be generated before each connection to a server.  Note this is not
enough to be completely anonymous, playing behind a VPN or proxy server is also
required.

Command `reply`
-----------------

Used to quickly reply to a received private message.  Works like `tell` but the
target client is the client who send the last private message to your client.
Reply will not not reply to players who don't have unique names (to avoid
confusion caused from impersonation).

Command `replymode`
-----------------

Works like `messagemode1` or `messagemode2`.  Used to quickly reply to a
message without using the console.  When this command is entered a `tell:
`-prompt shows up in the top left, with the target being the last client who
sent private message to your client.  The text entered will be sent privately
to the target client, as if the `tell` command was used.

Command `cmdmode`
-----------------

Works like `messagemode1` or `messagemode2`.  When this command is entered a
`]`-prompt shows up in the top left.  Then the user can enter a command that
will be executed when `Enter` is pressed.  When in this mode the user has
access to the editing commands that are available in the normal console.

Hor+ FOV
--------

The value of `cg_fov` specify the FOV used when playing with an aspect ratio of
4:3.  When playing on another aspect ratio, the game will preserve the same
vertical FOV as it would have on 4:3, but expand the horizontal FOV to fit the
screen.  Previously Vert- FOV was used.  See [this wikipedia article][fov] for
more information about Hor+ and Vert- FOV.

[fov]: https://en.wikipedia.org/wiki/Field_of_view_in_video_games

Editing text fields
-------------------

Additional keyboard commands have been added for editing text fields.  These
commands are available when typing commands in the in-game console, when
writing chat messages or using the tty-console.

The keyboard shortcuts are similar to the shortcuts common in UNIX shells or
common in general GUI software.

### Complete list of field editing keyboard shortcuts

General GUI-style keyboard shortcuts:

- `Left`, move cursor left one character
- `Right`, move cursor right one character
- `CTRL-Left`, move cursor left one word
- `CTRL-Right`, move cursor right one word
- `Home`, move cursor start of line
- `End`, move cursor end of line
- `Delete`, delete character after cursor
- `CTRL-Delete`, delete word after cursor
- `Backspace/CTRL-H`, delete character before cursor
- `CTRL-Backspace`, delete word before cursor
- `CTRL-V/SHIFT-INS`, paste from clipboard
- `CTRL-Z`, undo last change

UNIX commandline-style keyboard shortcuts (`^` is caret notation for control
character):

- `^A`, move cursor to start of line
- `^E`, move cursor to end of line
- `^U`, delete to beginning of line
- `^K`, delete to end of line
- `^D`, delete character after cursor
- `^C`, clear field, this operation cannot be undone
- `ALT-D`, delete word after cursor
- `Backspace/^H`, delete character before cursor
- `ALT-Backspace`/`ALT-^H`, delete word before cursor
- `^W`, delete previous "large word"
- `^B`, move cursor left one character
- `ALT-B`, move cursor left one word
- `^F`, move cursor right one character
- `ALT-F`, move cursor right one word
- `ALT-F`, move cursor right one word
- `^T`, transpose characters
- `ALT-T`, transpose words
- `^_`, undo last change
- `^Y`, yank from kill ring
- `ALT-Y`, rotate kill ring

### Complete list of console specific shortcuts

Console specific GUI-style keyboard shortcuts:

- `Up`, go to back in command in history
- `Down`, go to forward in command in history
- `CTRL-PgUp`, scroll up one line
- `CTRL-PgDn`, scroll down one line
- `CTRL-Home`, scroll to first line
- `CTRL-End`, scroll to last line
- `ALT-P`/`ALT-Left`, previous console tab
- `ALT-N`/`ALT-Right`, next console tab
- `ALT-`*`Digit`*, console tab number *`Digit`*

Console specific UNIX commandline-style keyboard shortcuts:

- `^P`, go back in command in history
- `^F`, go forward in command in history
- `^L`, clear console screen

Improved line editing in tty-console
------------------------------------

When running ioquake3 or OpenArena it is also possible to enter commands
through a tty-console.  This is especially useful for dedicated servers without
an in-game console.  Previously the editing-functionality was limited to
entering characters, and recalling previously entered commands in history.

This has been improved so the user has access to almost all keyboard commands
that are available in the in-game console.  The commands not available are
`CTRL-Del` and `CTRL-Backspace`, and console-specific commands for scrolling.

In the tty-console, the keyboard commands using ALT is accessed by prefixing
the key with `ESC`.  So `ESC-b` is equivalent to `ALT-b` in the in-game
console.  Most terminal emulators can be configured to make `ALT` send
`ESC`-codes in this way.

Tabbed console with filtered messages
-------------------------------------

A very convenient feature originally implemented in L0neStarr's fx3-engine.

When opening the console, there are 5 tabs at the bottom (named `all`, `sys`,
`chat`, `tchat`, and `tell`).  You can change console tabs using the shortcuts
listed above.  Each console has a specific purpose and their differences are
summarized in the following table.

| Number | Name    | Messages displayed | Command-type              |
| ------ | ------- | ------------------ | ------------------------- |
| 1      | `all`   | All messages       | Depends on `con_autochat` |
| 2      | `sys`   | Non-chat messages  | Command                   |
| 3      | `chat`  | All-chat messages  | `say`                     |
| 4      | `tchat` | Team-chat messages | `say_team`                |
| 5      | `tell`  | Private messages   | Reply to message          |

The `all` console works just like the normal quake console.  It will display
all messages and is the default console when the client starts.  If
`con_autochat` is non-zero, lines typed in this console without a preceding
slash will be interpreted as chat commands.

In the `sys` console all lines are interpreted as commands, independent of
`con_autochat`.

In the `chat` and `tchat` consoles, all lines typed will be interpreted as chat
commands.  So the line `Hello, world!` will become the command `say Hello,
world!` in the `chat` console or `say_team Hello, world!` in the `tchat`
console.  This is true even if the line contains a preceding slash.

In the `tell` console, lines will be interpreted as a command to reply to the
last received message.  So the line `Hello, world!` will become the command
`reply Hello, world!`.  This is true even if the line contains a preceding
slash.

The console filtering is especially useful for keeping chat history through map
switches, as all the system messages will not get displayed in the chat
consoles.  Similarly chat messages will not get displayed in the `sys` console.

The `condump`-command takes an optional parameter specifying which console to
dump text from.  The parameter can be the index (starting at 0) of the console,
or the name of the console.  If this optional parameter is omitted, then the
`all` console is assumed.
