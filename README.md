### Overview

SugaR is a free UCI chess engine derived from Stockfish. It is
not a complete chess program and requires some UCI-compatible GUI
(e.g. XBoard with PolyGlot, eboard, Arena, Sigma Chess, Shredder, Chess
Partner, Aquarium or Fritz) in order to be used comfortably. Read the
documentation for your GUI of choice for information about how to use
SugaR with it.

This version of SugaR supports up to 128 cores. The engine defaults
to one search thread, so it is therefore recommended to inspect the value of
the *Threads* UCI parameter, and to make sure it equals the number of CPU
cores on your computer.

This version of SugaR has support for Syzygybases.


### Files

This distribution of SugaR consists of the following files:

  * Readme.md, the file you are currently reading.

  * Copying.txt, a text file containing the GNU General Public License.

  * source, a subdirectory containing the full source code, including a Makefile
    that can be used to compile SugaR on Unix-like systems.

## Uci options

### Hash Memory

#### Hash

_Integer, Default: 16, Min: 1, Max: 131072 MB (64-bit) : 2048 MB (32-bit)_


The amount of memory to use for the hash during search, specified in MB (megabytes). This
number should be smaller than the amount of physical memory for your system.
A modern formula to determine it is the following:

_(T x S / 100) MB_
where
_T = the average move time (in seconds)
S = the average node speed of your hardware_
A traditional formula is the following:
_(N x F x T) / 512_
where
_N = logical threads number
F = clock single processor frequency (MB)
T = the average move time (in seconds)_

#### Save/Load Hash File Capability
Oribinal code by by Daniel José Queraltó
Interesting discussion here:
http://www.talkchess.com/forum3/viewtopic.php?f=2&t=64720&hilit=Stockfish+version+with+hash+saving+capability+EPD

The capability of saving the full hash to file, to allow the user to recover a previous analysis session and continue it.
The saved hash file will be of the same size of the hash memory, so if you defined 4 GB of hash, such will be the file size. Saving and loading such big files can take some time.

UCI options parameters:

-option name NeverClearHash type check default false
-option name HashFile type string default hash.hsh
-option name SaveHashtoFile type button
-option name LoadHashfromFile type button
-option name LoadEpdToHash type button  (First you set HashFile to an epd file and then press this new button.)

You can set the NeverClearHash option to avoid that the hash could be cleared by a Clear Hash or ucinewgame command.
The HashFile parameter is the full file name with path information. If you don't set the path, it will be saved in the current folder. It defaults to hash.hsh.
To save the hash, stop the analysis and press the SaveHashtoFile button in the uci options screen of the GUI.
To load the hash file, load the game you are interested in, load the engine withouth starting it, and press the LoadHashfromFile button in the uci options screen of the GUI. Now you can start the analysis.
### Analysis Contempt

This option has no effect in the playing mode.
A non-zero contempt is determined by Shashin's options and used only during game play, not during infinite analysis where it's turned off.
This helps make analysis consistent when switching sides and exploring various lines and lets you include a non-zero Contempt in your analysis. 
Note when playing against the computer, if you wish to use a non-zero Contempt, either turn off 'White Contempt' so that Contempt will apply to the Computer's side, or you can use the above description to set an appropriate Contempt for the specific side that SugaR is playing. 
Please note if 'White Contempt' is off, in infinite search or analysis mode, SugaR will always use a value of 0 for Contempt.
If you use this option, you can analyse with contempt settled for white, black or for all points of view.
Obviously, this option can produce an asymmetry in the evaluations (the evaluation changes when you switch sides). So, be aware!

#### Skill Level
Lower the Skill Level in order to make Stockfish play weaker (see also UCI_LimitStrength). Internally, MultiPV is enabled, and with a certain probability depending on the Skill Level a weaker move will be played.
										

* #### UCI_LimitStrength
Enable weaker play aiming for an Elo rating as set by UCI_Elo. This option overrides Skill Level.

* #### UCI_Elo
If enabled by UCI_LimitStrength, aim for an engine strength of the given Elo. This Elo rating has been calibrated at a time control of 60s+0.6s and anchored to CCRL 40/4.


## S_XPrO-NN can use two parallel books
original code by Thomas Zipproth:
https://zipproth.de/Brainfish/brainfish/

### NN section (Experimental Neural Networks inspired technics)
Experimental, MonteCarloTreeSearch, if activated, the engine's behaviour is similar to AlphaZero concepts.
Idea are implemented, integrated on SugaR:
	
### NN Persisted Self-Learning
_Boolean, Default: True_


- [https://github.com/amchess/BrainLearn] S_XPrO-NN implements a persistent learning algorithm, managing a file named experience.bin by Kelly kyniama and Andrea Manzo.

It is a collection of one or more positions stored with the following format (similar to in memory Stockfish Transposition Table):

- _best move_
- _board signature (hash key)_
- _best move depth_
- _best move score_

This file is loaded in an hashtable at the engine load and updated each time the engine receive quit or stop uci command.
When S_XPrO-NN starts a new game or when we have max 8 pieces on the chessboard, the learning is activated and the hash table updated each time the engine has a best score
at a depth >= 4 PLIES, according to Stockfish aspiration window.

At the engine loading, there is an automatic merge to experience.bin files, if we put the other ones, based on the following convention:

&lt;fileType&gt;&lt;qualityIndex&gt;.bin

where

- _fileType=&quot;experience&quot;/&quot;bin&quot;_
- _qualityIndex_ , an integer, incrementally from 0 on based on the file&#39;s quality assigned by the user (0 best quality and so on)

N.B.

Because of disk access, to be effective, the learning must be made at no bullet time controls (less than 5 minutes/game).

### Live Book section (thanks to Andrea Manzo "author di ShashChess" for explanations windows builds)

#### Live Book (checkbox)

_Boolean, Default: False_ If activated, the engine uses the livebook as primary choice.

#### Live Book URL
The default is the online chessdb [https://www.chessdb.cn/queryc_en/](https://www.chessdb.cn/queryc_en/), a wonderful project by noobpwnftw (thanks to him!)
 
[https://github.com/noobpwnftw/chessdb](https://github.com/noobpwnftw/chessdb)
[http://talkchess.com/forum3/viewtopic.php?f=2&t=71764&hilit=chessdb](http://talkchess.com/forum3/viewtopic.php?f=2&t=71764&hilit=chessdb)


#### Live Book Timeout

_Default 5000, min 0, max 10000_

#### Live Book Diversity

_Boolean, Default: False_ If activated, the engine varies its play, reducing conversely its strength because already the live chessdb is very large.

#### Live Book Contribute

_Boolean, Default: False_ If activated, the engine sends a move, not in live chessdb, in its queue to be analysed. In this manner, we have a kind of learning cloud.
### Syzygybases

**Configuration**

Syzygybases are configured using the UCI options "SyzygyPath",
"SyzygyProbeDepth", "Syzygy50MoveRule" and "SyzygyProbeLimit".

The option "SyzygyPath" should be set to the directory or directories that
contain the .rtbw and .rtbz files. Multiple directories should be
separated by ";" on Windows and by ":" on Unix-based operating systems.
**Do not use spaces around the ";" or ":".**

Example: `C:\tablebases\wdl345;C:\tablebases\wdl6;D:\tablebases\dtz345;D:\tablebases\dtz6`

It is recommended to store .rtbw files on an SSD. There is no loss in
storing the .rtbz files on a regular HD.

Increasing the "SyzygyProbeDepth" option lets the engine probe less
aggressively. Set this option to a higher value if you experience too much
slowdown (in terms of nps) due to TB probing.

Set the "Syzygy50MoveRule" option to false if you want tablebase positions
that are drawn by the 50-move rule to count as win or loss. This may be useful
for correspondence games (because of tablebase adjudication).

The "SyzygyProbeLimit" option should normally be left at its default value.

**What to expect**
If the engine is searching a position that is not in the tablebases (e.g.
a position with 8 pieces), it will access the tablebases during the search.
If the engine reports a very large score (typically 123.xx), this means
that it has found a winning line into a tablebase position.

If the engine is given a position to search that is in the tablebases, it
will use the tablebases at the beginning of the search to preselect all
good moves, i.e. all moves that preserve the win or preserve the draw while
taking into account the 50-move rule.
It will then perform a search only on those moves. **The engine will not move
immediately**, unless there is only a single good move. **The engine likely
will not report a mate score even if the position is known to be won.**

It is therefore clear that behaviour is not identical to what one might
be used to with Nalimov tablebases. There are technical reasons for this
difference, the main technical reason being that Nalimov tablebases use the
DTM metric (distance-to-mate), while Syzygybases use a variation of the
DTZ metric (distance-to-zero, zero meaning any move that resets the 50-move
counter). This special metric is one of the reasons that Syzygybases are
more compact than Nalimov tablebases, while still storing all information
needed for optimal play and in addition being able to take into account
the 50-move rule.


### Compiling it yourself

On Unix-like systems, it should be possible to compile SugaR
directly from the source code with the included Makefile.

SugaR has support for 32 or 64-bit CPUs, the hardware POPCNT
instruction, big-endian machines such as Power PC, and other platforms.

On Windows-like systems, it should be possible to compile SugaR
directly from the source code with the included Sugar.sln with Visual Studio 15.3 Community 
from GUI or with command scenario using Visual Studio 15.3 Community Commands Shell.

In general it is recommended to run `make help` to see a list of make
targets with corresponding descriptions. When not using the Makefile to
compile you need to manually
set/unset some switches in the compiler command line or use MSVC solution and project files provided; see file *types.h*
for a quick reference.


### Terms of use

SugaR is free, and distributed under the **GNU General Public License**
(GPL). Essentially, this means that you are free to do almost exactly
what you want with the program, including distributing it among your
friends, making it available for download from your web site, selling
it (either by itself or as part of some bigger software package), or
using it as the starting point for a software project of your own.

The only real limitation is that whenever you distribute SugaR in
some way, you must always include the full source code, or a pointer
to where the source code can be found. If you make any changes to the
source code, these changes must also be made available under the GPL.

For full details, read the copy of the GPL found in the file named
*Copying.txt*.

