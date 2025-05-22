# Introduction
Quantum Chess is a variant of chess that gives players some new movement options, allowing them to create quantum superposition, where the pieces can exist on more than one square at the same time. This page, and associated resources, documents the official rules and implementation details for the game.

# Quantum Pieces
Pieces can exist in a superposition of both occupying and not occupying a given square. This superposition of occupancy is encoded in the [quantum state](./math#quantum-state) of the board.

# Movement
Quantum Chess moves are described by “source” squares and “target” squares. All movement is accomplished through [unitary evolution](./math#movement-unitaries) of the [quantum state](./math#quantum-state).

For sliding moves, squares that are in superposition in the path are considered as empty when determining whether the move is legal.

Moves are defined by a [move type] and a move variant.

# Move Types
There are three types of moves that players have access to: [standard](#standard-move), [split](#split-move), and [merge](#merge-move). Pawns cannot perform split or merge moves.

## Standard Move
All pieces can perform moves equivalent to those in standard chess. Thus, the term standard is a catchall for a number of defined types:

* jump: knights and kings
* slide: rooks, bishops, and queens
* pawn step
* pawn two step
* pawn capture
* en passant
* king side castle
* queen side castle

Standard moves are designated by a single source and single target.

## Split Move
All **non-pawns** can perform a split move. The split move allows a player to create superposition. Split moves are designated by a single source square, and two different target squares that can be reached from that source by using a [standard jump](#standard-move), or a [standard slide](#standard-move). For example, a knight on b1 can split to a3 and c3.

The split move **can’t be used to capture**. Legal target squares must be either empty, or occupied by the same kind of piece as that being moved.

Special movement rules, like castling, are not supported when chosing split move targets.

## Merge Move
All **non-pawns** can perform a merge move. The merge move allows a player to attempt to "undo" a split. Merge moves are designated by two source squares, and a single square that can be reached, from both sources, by using a [standard jump](#standard-move), or a [standard slide](#standard-move). For example, a knight that has split to occupy a3 and c3 can attempt to merge to b5.

The merge move **can’t be used to capture**. Legal target squares must be either empty, or occupied by the same kind of piece as that being moved.

Special movement rules, like castling, are not supported when chosing split move targets.


# Move Variants
Quantum Chess allows moves where the target square is in superposition of being occupied a unoccupied. This gives rise to scenarios that are not considered in standard chess. The concept of a move variant is introduced to help define the rules for these more exotic states. The variant of a move is determined by the state of the target(s) of a move.

## Basic Variant
The basic variant is a move where the target square(s) is(are) unoccupied, or occupied by the same kind piece as that being moved.

## Excluded Variant
The excluded variant is a move where the target square(s) is(are) occupied by a piece that **can't** be captured. For example, if the target is occupied by of the same color, but different type, or if the piece being moved is a pawn stepping forward.

## Capture Variant
The capture variant is a move where the target square(s) is(are) occupied by a piece that **can** be captured. This includes almost any move where the target(s) is(are) occupied by any opponent's piece, with exceptions for special capturing rules for pawns, and castling.

# Moves
This section lists all of the legal move types and variants in Quantum Chess. It gives the pieces that have access to them, the conditions under which they're legal, and the quantum circuit used to execute them in the game (for those who are interested). Any move type or variant not listed here is considered illegal.

## Basic Jump
Pieces: Knight, King (non-castling)

[Circuit](./math#basic-jump)

Conditions:
* The target square is unoccupied, or occupied by the same kind of piece as the source.

## Excluded Jump
Pieces: Knight, King (non-castling)

[Circuit](./math#excluded-jump)

Conditions:
* The target square is occupied, in superposition, by a piece that is the same color, but different type, than the source.

## Capture Jump
Pieces: Knight, King (non-castling)

[Circuit](./math#capture-jump)

Conditions:
* The target square is occupied by an opponent's piece.

## Basic Slide
Pieces: Bishop, Rook, Queen

[Circuit](./math#basic-slide)

Conditions:
* The target square is unoccupied, or occupied by the same kind of piece as the source.

## Excluded Slide
Pieces: Bishop, Rook, Queen

[Circuit](./math#excluded-slide)

Conditions:
* The target square is occupied, in superposition, by a piece that is the same color, but different type, than the source.

## Capture Slide
Pieces: Bishop, Rook, Queen

[Circuit](./math#capture-slide)

Conditions:
* The target square is occupied by an opponent's piece.

## Basic Pawn Step
Pieces: Pawn

[Circuit](./math#basic-pawn-step)

Conditions:
* The target square is one square in the "forward" direction.
* The target square is unoccupied, or occupied by a same color pawn.

## Excluded Pawn Step
Pieces: Pawn

[Circuit](./math#excluded-pawn-step)

Conditions:
* The target square is one square in the "forward" direction.
* The target square is occupied, in superposition, by any other kind of piece.

## Basic Pawn Two Step
Pieces: Pawn

[Circuit](./math#basic-pawn-two-step)

Conditions:
* The source square is the moving pawn's starting position.
* The target square is two squares in the "forward" direction
* The target square is unoccupied, or occupied by a same color pawn.

## Excluded Pawn Two Step
Pieces: Pawn

[Circuit](./math#excluded-pawn-two-step)

Conditions:
* The source square is the moving pawn's starting position.
* The target square is two squares in the "forward" direction
* The target square is occupied, in superposition, by any other kind of piece.

## Pawn Capture
Pieces: Pawn

[Circuit](./math#pawn-capture)

Conditions:
* The target square is one square in the "forward-right" or "forward-left" direction.
* The target square is occupied by an opponent's piece.

## Basic En Passant
Pieces: Pawn

[Circuit](./math#basic-en-passant)

Conditions:
* The source square is adjacent to an opponent's pawn that moved forward two steps during the opponent's previous turn.
* The target square is directly behind the opponent's pawn being captured via en passant.
* The target square is unoccupied, or occupied by a same color pawn.

## Excluded En Passant
Pieces: Pawn

[Circuit](./math#excluded-en-passant)

Conditions:
* The source square is adjacent to an opponent's pawn that moved forward two steps during the opponent's previous turn.
* The target square is directly behind the opponent's pawn being captured via en passant.
* The target square is or occupied by a non-pawn of the same color.

## Capture En Passant
Pieces: Pawn

[Circuit](./math#capture-en-passant)

Conditions:
* The source square is adjacent to an opponent's pawn that moved forward two steps during the opponent's previous turn.
* The target square is directly behind the opponent's pawn being captured via en passant.
* The target square is or occupied by an opponent's piece

## Basic Kingside Castle
Pieces: King

[Circuit](./math#basic-kingside-castle)

Conditions:
* The source square is the king's starting square.
* The target square is two squares to the right of the king (g file).
* Neither the king, nor the kingside rook, has been involved in any previous move.
* The target square is empty.
* The square between the source and target (f file), is either unoccupied, or occupied in superposition by a same color rook.

## Excluded Kingside Castle
Pieces: King

[Circuit](./math#excluded-kingside-castle)

Conditions:
* The source square is the king's starting square.
* The target square is two squares to the right of the king (g file).
* Neither the king, nor the kingside rook, has been involved in any previous move.
* The target square and/or the square between the source and target (f file), is occupied by any other piece.

## Basic Queenside Castle
Pieces: King

[Circuit](./math#basic-queenside-castle)

Conditions:
* The source square is the king's starting square.
* The target square is two squares to the left of the king (c file).
* Neither the king, nor the queenside rook, has been involved in any previous move.
* The target square is empty.
* The square between the source and target (d file), is either unoccupied, or occupied in superposition by a same color rook.

## Excluded Queenside Castle
Pieces: King

[Circuit](./math#excluded-queenside-castle)

Conditions:
* The source square is the king's starting square.
* The target square is two squares to the right of the king (c file).
* Neither the king, nor the kingside rook, has been involved in any previous move.
* The target square and/or the square between the source and target (d file), is occupied by any other piece.

# Measurement
Measurement is when the game engine answers certain questions about the quantum state, such as “Is square a3 occupied”. This can cause the amount of superposition to be reduced, for the entire board, which is often referred to as collapse. Measurement outcomes are randomly chosen, based on the probabilities of the occupancies involved.

The outcome of a measurement determines whether the move will continue. **A measurement outcome that doesn't allow a move to continue still counts as the player's turn**, because the state of the game is changed by the measurement.

The [excluded](#excluded-variant) and [capture](#capture-variant) variants of each type of move require measurements. The measurements are designed to allow the engine to unambiguously assign a piece to the target square(s) after the move procedure is complete. The details of the various measurements can be seen in the [move circuits](./math#move-circuits).

# Capture the King
In Quantum Chess you win by capturing the opponent’s king. This means 100% captured. If both players still have a king with any non-zero probability, the game continues. There is no concept of check. This means that any standard chess movement restrictions, that rely on check or checkmate, do not apply.

# Gameplay
1. The player designates the type of move they wish to perform.
2. The engine applies the appropriate [quantum circuit](./math#move-circuits) to the quantum state, and updates the classical information, to be consistent with any collapse and changes in square occupancy.
3. If any [gameover conditions](#gameover-conditions) are met, the game ends. Otherwise the player's turn ends, and the ply is incremented.

## Gameover Conditions
* White Win: Black has no king, but white does
* Black Win: White has no king, but black does
* Draw: Neither player has a king
* 50-Move Rule: There is a 50-move rule counter which starts at 0 and increases by 1 every time either player makes a move. The counter resets to 0 each time the number of pieces, as determined by the sum of square probabilities over all squares on the board, becomes different by at least 1 from the number of pieces the last time the counter was reset.
If the counter reaches 100, then every square on the board is measured and the game is decided as follows: if both kings remain on the board it is a draw; otherwise the result is determined by one of the conditions listed above.
* Threefold Repetition: In certain limited circumstances, it may be possible to detect that an identical position has previously occurred. If the current position has occurred at least twice previously, then the game may end according to the threefold repetition rule. If so, the game's result is determined in the same way as with the 50-move rule.
