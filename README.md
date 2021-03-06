# The Game of Quadris
Quadris is a Latinization of the game Tetris.

A game of Quadris consists of a board, 11 columns wide and 15 rows high. 

Blocks consisting of four cells (tetrominoes) appear at the top of the screen, and you must drop them onto the board so as not to leave any gaps. 

Once an entire row has been filled, it disappears, and the blocks above move down by one unit.

Quadris differs from Tetris in one significant way: it is not real-time. You have as much time as you want to decide where to place a block.

The major components of the system are as follows:

# Blocks

There are seven types of blocks, shown below with their initial configurations:

         J     L OO  SS ZZ TTT        
    IIII JJJ LLL OO SS   ZZ T

# Board

The board contains 11 columns and 15 rows. 

There are three extra rows (total 18) at the top of the board to give room for blocks to rotate, without falling off the board. 

If a block is at the extreme right-hand side of the board, to the extent that there is no horizontal room to rotate it, it can’t be rotated.

When a block shows up on the board, it appears in the top-left corner, just below the three reserve rows. If there is no room for the block in this position, the game is over.

When a block is dropped onto the board, any rows have been completely filled as a result of the block having dropped is removed from the board, and the remaining blocks above these rows move down to fill the gap.

# Display

There is a text-based display and a graphical display of the game board. 

A sample text display follows:

    Level: 1
    Score: 0
	Hi Score: 100
	-----------
	TTT
	T
	S
	ZSS
	ZZIS
	ZJI
	OO JI
	IIII OOJJI
	-----------
	Next:
	L
	LLL

# Next Block

The level of difficulty of the game depends on the policy for selecting the next block.

• Level 0: Takes its blocks in sequence from the file sequence.txt (a sample is provided), or from another file, whose name is supplied on the command line. This level is non-random, and can be used to test with a predetermined set of blocks. 

• Level 1: The block selector will randomly choose a block with probabilities skewed such that S and Z blocks are selected with probability 1/12 each, and the other blocks are selected with probability 1/6 each.

• Level 2: All blocks are selected with equal probability.

• Level 3: The block selector will randomly choose a block with probabilities skewed such that S and Z blocks are selected with probability 2/9 each, and the other blocks are selected with probability 1/9 each. Moreover, blocks generated in level 3 are “heavy”: every command to move or rotate the block will be followed immediately and automatically by a downward move of one row (if possible).

• Level 4: In addition to the rules of Level 3, in Level 4 there is an external constructive force: every time you place 5 (and also 10, 15, etc.) blocks without clearing at least one row, a 1x1 block (indicated by * in text, and by the colour brown in graphics) is dropped onto the game board in the centre column. Once dropped, it acts like any other block: if it completes a row, the row disappears. So if you do not act quickly, these blocks will work to eventually split the screen in two, making the game difficult to play.

# Command Interpreter

You interact with the system by issuing text-based commands. The following commands are supported:

• left moves the current block one cell to the left. If this is not possible (left edge of the board, or block in the way), the command has no effect.

• right as above, but to the right.

• down as above, but one cell downward.

• clockwise rotates the block 90 degrees clockwise, as described earlier. If the rotation cannot be accomplished without coming into contact with existing blocks, the command has no effect.

• counterclockwise as above, but counterclockwise.

• drop drops the current block. It is (in one step) moved downward as far as possible until it comes into contact with either the bottom of the board or a block. This command also triggers the next block to appear. Even if a block is already as far down as it can go (as a result of executing the down command), it still needs to be dropped in order to get the next block.

• levelup Increases the difficulty level of the game by one. The block showing as next still comes next, but subsequent blocks are generated using the new level. If there is no higher level, this command has no effect.

• leveldown Decreases the difficulty level of the game by one. The block showing as next still comes next, but subsequent blocks are generated using the new level. If there is no lower level, this command has no effect.

• norandom file Relevant only during levels 3 and 4, this command makes these levels nonrandom, instead taking input from the sequence file, starting from the beginning. This is to facilitate testing.

• random Relevant only during levels 3 and 4, this command restores randomness in these levels.

• sequence file Executes the sequence of commands found in file. This is to facilitate the construction of test cases.

• I, J, L, etc. Useful during testing, these commands replace the current undropped block with the stated block. Heaviness is detemined by the level number. Note that, for heavy blocks, these commands do not cause a downward move.

• restart Clears the board and starts a new game.

• hint Suggests a landing place for the current block. The game suggests the best place to put the current block, but it does not suggest a position that is not legally reachable (for example, any position that cannot be reached without moving the block upwards). The hint is on the text display by a block made of ?’s in the suggested position, and in the graphics display using the colour black. On the very next command, no matter what the command is, the hint disappears from the displays.

End-of-file (EOF) terminates the game.

Only as much of a command as is necessary to distinguish it from other commands needs to be entered. For example, lef is enough to distinguish the left command from the levelup command, so the system understands either lef or left as meaning left.

In addition, commands can take a multiplier prefix, indicating that that command should be executed some number of times. For example 3ri means move to the right by three cells. If, for example, it is only possible to move to the right by two cells, then the block should move to the right by two cells. Similarly, 2levelu means increase the level by two. Prefixes of 0 or 1 are permitted, and mean that the comand should be run 0 times and 1 time, respectively. It is valid to apply a multiplier to the drop command. The command 3dr (or 3dro or 3drop)) means drop the current block and then drop the following two blocks from their default position. If blocks are heavy, a multiplied command is still followed by only own downword move (example: the command 3left moves the block three positions left, and then one position down). It is not valid to apply a multiplier to the restart, hint, norandom, or random commands (if a multiplier is supplied, it would have no effect).

# Scoring

The game is scored as follows: when a line (or multiple lines) is cleared, you score points equal to (current level, plus number of lines) squared. (For example, clearing a line in level 2 is worth 9 points.) 

In addition, when a block is completely removed from the screen (i.e., when all of its cells have disappeared) you score points equal to the level you were in when the block was generated, plus one, squared. (For example if you got an O-block while on level 0, and cleared the O-block in level 3, you get 1 point.)

The game tracks the current score and the hi score. When the current score exceeds the hi score, the hi score is updated so that it matches the current score. When the game is restarted, the current score reverts to 0, but the hi score persists until the program terminates.

# Command-line Interface

The program supports the following options on the command line:

• -text runs the program in text-only mode. No graphics are displayed. The default behaviour (no -text) is to show both text and graphics.

• -seed xxx sets the random number generator’s seed to xxx. If you don’t set the seed, you always get the same random sequence every time you run the program. It’s good for testing, but not much fun.

• -scriptfile xxx Uses xxx instead of sequence.txt as a source of blocks for level 0.

• -startlevel n Starts the game in level n. The game starts in level 0 if this option is not supplied.
