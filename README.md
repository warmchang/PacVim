# PacVim

PacVim is a game that teaches you vim commands.
You must move pacman (the green cursor) to highlight each word on the gameboard while avoiding the ghosts (in red).

![my image](https://raw.githubusercontent.com/jmoon018/PacVim/master/gifs/all.gif)
<h2>Building and running</h2>

Vim is a great tool to write and edit code, but many 
people, including me, struggled with the steep learning curve. 
I did not find a fun, free way to learn about the vim commands
in-depth, and thus, PacVim was born. Inspired by the the classic,
PacMan, <b>PacVim</b> is a game that'll give anyone plenty of
practice with the vim commands while being a ton of fun to play.

Download and build the game with:
```
$ git clone https://github.com/jmoon018/PacVim.git
$ cd PacVim
$ make
```

To play, run:
```
$ ./pacvim
```

#How To Play

The objetive of PacVim is very similar to PacMan.
You must run over all the characters on the screen while avoiding the ghosts (red `G`).
PacVim has two special obstacles:

1. You cannot move into the walls (yellow color).  You must use vim motions to jump over them.

2. If you step on a tilde character (cyan `~`), you lose!

You are given three lives. You gain a life each time you beat
level 0, 3, 6, 9, etc. There are 10 levels, 0 through 9. After
beating the 9th level, the game is reset to the 0th level, but
the ghosts move faster.

<b>Winning conditions:</b> Use vim commands to move the cursor
over the letters and highlight them. After all letters are
highlighted, you win and proceed to the next level.

<b>Losing conditions:</b> If you touch a ghost (indicated
by a red `G`) or a tilde character, you lose a life. If you
have less than 0 lives, you lose the entire game.

<h4>List of Implemented Commands</h4>

| key | what it does |
| --- | --- |
| h   | move left |
| j   | move down |
| k   | move up |
| l   | move right |
| w   | move forward to next word beginning |
| W   | move forward to next WORD beginning |
| e   | move forward to next word ending |
| E   | move forward to next WORD ending |
| b   | move backward to next word beginning |
| B   | move backward to next WORD beginning |
| $   | move to the end of the line |
| 0   | move to the beginning of the line |
| gg/1G | move to the beginning of the first line |
| *number*G | move to the beginning of the line given by *number* |
| G   | move to the beginning of the last line |
| ^   | move to the first word at the current line |
| &   | 1337 cheatz (beat current level)


<h2>Code Overview</h2>

<h4>avatar.cpp</h4>
Contains the <b>`avatar`</b> class, which contains information about
the player, such as his/her x position, y position, etc. It
also contains methods that allow the player to move and correspond
to the keystrokes. For example, the <b>`avatar`</b> class contains the method
called <b>`parseWordForward(bool)`</b> which implements the functionality
for the "w" (or "W" if true) vim command.

<h4>ghost1.cpp</h4>
Contains the <b>Ghost1</b> class, derived from the <b>`avatar`</b> class. It is
just like the avatar class, but it requires an extra paremeter
upon initialization, called `sleepTime`, a double value that
determines how quickly a ghost moves. It refers to the time, in
seconds, the ghost must wait to move. A `sleepTime` of 0.5 means
the ghost moves 2 times a second. `sleepTime` = 0.33 is 3 moves per second, etc.
<br>
The `Ghost1` class also contains a method called <b>`spawnGhost`</b> which
creates the ghost at the location based on its initialization parameters.
The ghost will appear when `READY` (global bool) is true (this means the player
is ready), and it will call <b>`ghost.think()`</b> one second afterwards.
<br>
`think` is a recursive method that simply moves the ghost. It uses 
a basic greedy algorithm based on the distance of the ghost's potential
moves (up, down, right, left) and the player.
<br>

Each ghost contains its own thread. A global mutex, called `mtx`, is
used (in `think`) to ensure that resources are shared properly.

`helperFns.cpp`
Contains methods that allow easy changes of the screen. A few of them:

* `chtype charAt(int x, int y)` returns the chtype at the (x,y) location
* `bool writeAt(int x, int y, chtype letter)` writes the 'letter' at location (x,y). Returns false if location is invalid.
* `void printAtBottom(string msg)`  writes a message one line below the last line

<h4>game.cpp</h4>
This contains the <i>main()</i> method among many other important ones

<b>main</b> - contains a loop that breaks when `LIVES` < 0. In the loop,
the proper map name is determined and loaded. Data is reset (such as as the pointers,
the ghost AI, etc). The level is incremented.
<br>
<b>init(const char*)</b> - called by <b>`main`</b>. Calls <b>`drawScreen(str map)`</b>, creates and
spawns player and ghosts threads. Then calls <b>`playGame`</b>. After <b>`playGame`</b>
ends, all the ghost threads are deleted, and then we go back to the <b>`main`</b> method.
<br>
<b>drawScreen(char* map)</b> - called by <b>`init`</b>. Reads from text file given
by parameter. Loads everything onto the screen with the proper color and gets
information from the ghost and player so that they spawn in the proper place in <b>`init`</b>.
<br>
<b>playGame(time_t, avatar player)</b> - called by <b>`init`</b>. This contains two loops,
one that consumes everything in the input buffer (which is then deleted), the second
loop allows the player to continuously input keystrokes. When a keystroke is input,
<b>`onKeystroke`</b> is called
<br>


<h2><a name="ToDoBugsTag">To-dos / Bugs</a></h2>
<ul>
	<li>More testing on `#G` and `G` commands</li>
	<li>Change the map-making process easier</li>
	<li>Refactor code, more comments</li>
</ul>
