# Introduction
The Universal Quantum Chess Interface (UQCI) protocol attempts to emulate and extend the well known UCI protocol used by chess engines. The full UCI Protocol description can be found at [wbec-ridderkerk.nl/html/UCIProtocol.html](http://wbec-ridderkerk.nl/html/UCIProtocol.html). Most of the following is copied directly from that site.

# Description

* The specification is independent of the operating system. For Windows,
  the engine is a normal exe file, either a console or "real" windows application.

* All communication is done via standard input and output with text commands,

* The engine should boot and wait for input from the GUI,
  the engine should wait for the "isready" or "setoption" command to set up its internal parameters
  as the boot process should be as quick as possible.

* The engine must always be able to process input from stdin, even while thinking.

* All command strings the engine receives will end with '\n',
  also all commands the GUI receives should end with '\n',
  Note: '\n' can be 0x0c or 0x0a0c or any combination depending on your OS.
  If you use Engine und GUI in the same OS this should be no problem if you communicate in text mode,
  but be aware of this when for example running a Linux engine in a Windows GUI.

* The engine will always be in forced mode which means it should never start calculating
  or pondering without receiving a "go" command first.

* Before the engine is asked to search on a position, there will always be a position command
  to tell the engine about the current position.

* By default all the opening book handling is done by the GUI,
  but there is an option for the engine to use its own book ("OwnBook" option, see below)

* If the engine or the GUI receives an unknown command or token it should just ignore it and try to
  parse the rest of the string.

* If the engine receives a command which is not supposed to come, for example "stop" when the engine is
  not calculating, it should also just ignore it.

# Move Format
The move format is in [Quantum Chess long algebraic notation](https://github.com/quantum-realm-games/QuantumChessEngine/blob/master/docs/qc_algebraic_notation.md).
A nullmove from the Engine to the GUI should be send as 0000.
Examples:  e2e4, b1^a3c3, a3c3^b5, b1wc3.m0, e1g1 (white short castling), e7e8q (for promotion)

# GUI to Engine Commands
<p align="center">
<img src="https://github.com/quantum-realm-games/QuantumChessEngine/blob/master/docs/images/uqci_engine_to_gui_commands.png" width="545" height="406"></img>
</p>

## uci

Tell engine to use the uci (universal chess interface).
This will be sent once as a first command after program boot
to tell the engine to switch to uci mode.
After receiving the uci command the engine must identify itself with the "id" command,
and send the "option" commands to tell the GUI which engine settings the engine supports if any.
After that the engine should sent "uciok"  to acknowledge the uci mode.
If no uciok is sent within a certain time period, the engine task will be killed by the GUI.

## debug [ on | off ]
Switch the debug mode of the engine on and off.
In debug mode the engine should sent additional infos to the GUI, e.g. with the "info string" command,
to help debugging, e.g. the commands that the engine has received etc.
This mode should be switched off by default and this command can be sent
any time, also when the engine is thinking.

## isready
This is used to synchronize the engine with the GUI. When the GUI has sent a command or
multiple commands that can take some time to complete,
this command can be used to wait for the engine to be ready again or
to ping the engine to find out if it is still alive.
This command is also required once before the engine is asked to do any search
to wait for the engine to finish initializing.
This command must always be answered with "readyok" and can be sent also when the engine is calculating
in which case the engine should also immediately answer with "readyok" without stopping the search.

## setoption name  [value ]
This is sent to the engine when the user wants to change the internal parameters
of the engine. For the "button" type no value is needed.
One string will be sent for each parameter and this will only be sent when the engine is waiting.
The name of the option in  should not be case sensitive and can inludes spaces like also the value.
The substrings "value" and "name" should be avoided in  and  to allow unambiguous parsing,
for example do not use  = "draw value".
The Quantum Chess engine supports the following options:
* **forceturn [true/false]**
Optionally play the game without turn order enforced.
Example: `setoption name forceturn value true`
* **forcewin [true/false]**
Optionally play the game without win conditions enforced.
Example: `setoption name forcewin value false`
* **forcemeasure [true/false]**
If set to true, move strings will force (post-select) measurement outcomes.
Example: `setoption name forcemeasure value true`
* **resturl "some url string"**
URL to be used for an external quantum resource that implements the [Quantum Chess REST API](https://github.com/quantum-realm-games/QuantumChessEngine/blob/master/docs/Quantum-Chess-REST-API). Provide the null string "" to clear a previous url.
Example: `setoption name resturl value https://www.my-quantum-chess-implementation.example`

## register
This is the command to try to register an engine or to tell the engine that registration
will be done later. This command should always be sent if the engine has sent "registration error" at program startup.

The following tokens are allowed:
* **later**
The user doesn't want to register the engine now.
* **name**
The engine should be registered with the name.
* **code**
The engine should be registered with the code.

Example: `register later`
Example: `register name Stefan MK code 4359874324`

## ucinewgame
This is sent to the engine when the next search (started with `position` and `go`) will be from
a different game. This can be a new game the engine should play or a new game it should analyze but
also the next position from a testsuite with positions only.
As the engine's reaction to `ucinewgame` can take some time the GUI should always send `isready`
after `ucinewgame` to wait for the engine to finish its operation.

## position [fen  | startpos ] moves  ...
Set up the classical position, described in fen string notation, on the internal board and play the moves.
If the game was played  from the start position the string `startpos` will be sent
Note: No "new" command is needed. However, if this position is from a different game than
the last position sent to the engine, the GUI should have sent a "ucinewgame" inbetween.

## go
Start calculating on the current position set up with the "position" command.
There are a number of commands that can follow this command, all will be sent in the same string.
If one of the following commands is not sent, the engine should interpret its value such that it not influence the search.
* **searchmoves  ...**
Restrict search to this moves only.
Example: After `position startpos` and `go infinite searchmoves e2e4 d2d4`,
the engine should only search the two moves e2e4 and d2d4 in the initial position.
* **ponder**
Start searching in pondering mode. This means that the last move sent in the position string is the ponder move.
The engine can do what it wants to do, but after a `ponderhit` command, it should execute the
suggested move to ponder on.
* **wtime x**
White has x milliseconds left on the clock
* **btime x**
Black has x milliseconds left on the clock
* **winc x**
White increment per move in milliseconds if x > 0
* **binc x**
Black increment per move in milliseconds if x > 0
* **movestogo x**
There are x moves to the next time control.
* **depth x**
Search x plies only.
* **nodes x**
Search x nodes only,
* **mate x**
Search for a mate in x moves
* **movetime x**
Search exactly x milliseconds
* **infinite**
Search until the "stop" command. Do not exit the search without being told in this mode!

## stop
Stop calculating as soon as possible. Don't forget the `bestmove` and possibly the `ponder` token when finishing the search.

## ponderhit
The user has played the expected move. This will be sent if the engine was told to ponder on the same move
the user has played. The engine should continue searching but switch from pondering to normal search.

## calcbell x y
Calculate the expected values for the 8-Bell operators, described [here](https://github.com/cantwellc/QuantumChessEngine/wiki/Bell-Measures), for the pair of squares x and y.

Example: `calcbell b1 c3` calculates the relative bell measures between squares b1 and c3.

## quit
Quit the program as soon as possible.

# Engine to GUI
## id
	* name
		this must be sent after receiving the "uci" command to identify the engine,
		e.g. "id name Shredder X.Y\n"
	* author
		this must be sent after receiving the "uci" command to identify the engine,
		e.g. "id author Stefan MK\n"

## uciok
	Must be sent after the id and optional options to tell the GUI that the engine
	has sent all infos and is ready in uci mode.

## readyok
	This must be sent when the engine has received an "isready" command and has
	processed all input and is ready to accept new commands now.
	It is usually sent after a command that can take some time to be able to wait for the engine,
	but it can be used anytime, even when the engine is searching,
	and must always be answered with "isready".
## bestmove  [ ponder  ]
	the engine has stopped searching and found the move  best in this position.
	the engine can send the move it likes to ponder on. The engine must not start pondering automatically.
	this command must always be sent if the engine stops searching, also in pondering mode if there is a
	"stop" command, so for every "go" command a "bestmove" command is needed!
	Directly before that the engine should send a final "info" command with the final search information,
	the the GUI has the complete statistics about the last search.

## copyprotection
	this is needed for copyprotected engines. After the uciok command the engine can tell the GUI,
	that it will check the copy protection now. This is done by "copyprotection checking".
	If the check is ok the engine should sent "copyprotection ok", otherwise "copyprotection error".
	If there is an error the engine should not function properly but should not quit alone.
	If the engine reports "copyprotection error" the GUI should not use this engine
	and display an error message instead!
	The code in the engine can look like this
      TellGUI("copyprotection checking\n");
	   // ... check the copy protection here ...
	   if(ok)
	      TellGUI("copyprotection ok\n");
      else
         TellGUI("copyprotection error\n");

## registration
	this is needed for engines that need a username and/or a code to function with all features.
	Analog to the "copyprotection" command the engine can send "registration checking"
	after the uciok command followed by either "registration ok" or "registration error".
	Also after every attempt to register the engine it should answer with "registration checking"
	and then either "registration ok" or "registration error".
	In contrast to the "copyprotection" command, the GUI can use the engine after the engine has
	reported an error, but should inform the user that the engine is not properly registered
	and might not use all its features.
	In addition the GUI should offer to open a dialog to
	enable registration of the engine. To try to register an engine the GUI can send
	the "register" command.
	The GUI has to always answer with the "register" command	if the engine sends "registration error"
	at engine startup (this can also be done with "register later")
	and tell the user somehow that the engine is not registered.
	This way the engine knows that the GUI can deal with the registration procedure and the user
	will be informed that the engine is not properly registered.

## info
	the engine wants to send infos to the GUI. This should be done whenever one of the info has changed.
	The engine can send only selected infos and multiple infos can be send with one info command,
	e.g. "info currmove e2e4 currmovenumber 1" or
	     "info depth 12 nodes 123456 nps 100000".
	Also all info belonging to the pv should be sent together
	e.g. "info depth 2 score cp 214 time 1242 nodes 2124 nps 34928 pv e2e4 e7e5 g1f3"
	I suggest to start sending "currmove", "currmovenumber", "currline" and "refutation" only after one second
	to avoid too much traffic.
	Additional info:
	* depth
		search depth in plies
	* seldepth
		selective search depth in plies,
		if the engine sends seldepth there must also a "depth" be present in the same string.
	* time
		the time searched in ms, this should be sent together with the pv.
	* nodes
		x nodes searched, the engine should send this info regularly
	* pv  ...
		the best line found
	* multipv
		this for the multi pv mode.
		for the best move/pv add "multipv 1" in the string when you send the pv.
		in k-best mode always send all k variants in k strings together.
	* score
		* cp
			the score from the engine's point of view in centipawns.
		* mate
			mate in y moves, not plies.
			If the engine is getting mated use negativ values for y.
		* lowerbound
	      the score is just a lower bound.
		* upperbound
		   the score is just an upper bound.
	* currmove
		currently searching this move
	* currmovenumber
		currently searching move number x, for the first move x should be 1 not 0.
	* hashfull
		the hash is x permill full, the engine should send this info regularly
	* nps
		x nodes per second searched, the engine should send this info regularly
	* tbhits
		x positions where found in the endgame table bases
	* cpuload
		the cpu usage of the engine is x permill.
	* string
		any string str which will be displayed be the engine,
		if there is a string command the rest of the line will be interpreted as .
	* refutation   ...
	   move  is refuted by the line  ... , i can be any number >= 1.
	   Example: after move d1h5 is searched, the engine can send
	   "info refutation d1h5 g6h5"
	   if g6h5 is the best answer after d1h5 or if g6h5 refutes the move d1h5.
	   if there is norefutation for d1h5 found, the engine should just send
	   "info refutation d1h5"
		The engine should only send this if the option "UCI_ShowRefutations" is set to true.
	* currline   ...
	   this is the current line the engine is calculating.  is the number of the cpu if
	   the engine is running on more than one cpu.  = 1,2,3....
	   if the engine is just using one cpu,  can be omitted.
	   If  is greater than 1, always send all k lines in k strings together.
		The engine should only send this if the option "UCI_ShowCurrLine" is set to true.
