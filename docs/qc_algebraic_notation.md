# Intro
The Quantum Chess engine supports an extension of [algebraic notation](https://en.wikipedia.org/wiki/Algebraic_notation_(chess)) used in chess. The current implementation is slightly incomplete, as fully abbreviated move strings are not supported yet. However, it is not truly considered "long algebraic notation" because some abbreviation is supported. Squares on the quantum chess board follow the same naming convention as standard chess.

![Board](/images/square_names.jpg){: .align-center}

# Standard Move
Standard moves are considered to be any legal movement pattern in standard chess. This includes castling and en passant. All standard moves are given by denoting a single source square and a single target square.

* `a2a4` denotes a move from a2 to a4.

`Pa2a4` is also a valid move string from a2 to a4, but adds the extra information that it is a white pawn being moved.

# Split Move
A split move requires the naming of a single "source" square and two "target" squares, separated by the `^` symbol.
* `b1^a3c3` denotes a split move from b1 to a3 and c3.

# Merge Move
A merge move requires the naming of two "source" squares and a single "target" square, separated by the `^` symbol.

* `a3c3^b5` denotes a merge move from a3 and c3 to b5.

# Move Variants
The Quantum Chess algebraic notation defines the following notation for the three variants described in [Rules](/rules#move-variants).
## Basic
A basic variant is denoted with just the raw move string, no extra characters.
* `a2a4` denotes a basic variant of the move from a2 to a4.
## Capture
A capture variant is denoted with `x`, in keeping with standard chess algebraic notation.
* `b1xc3` denotes a capture variant of the move from b1 to c3.
## Excluded
An excluded variant is denoted with `w`.
* `b1wc3` denotes an excluded variant of the move from b1 to c3.

The current ruleset does not support capture or excluded variants for split and merge moves. If this changes in the future, the recommended notation would be to place the variant symbol to the left of the target square that is defining the variant. E.g. `b1^xa3wc3` or `a3c3^wb5`.

# Measurement
Excluded and capture variants force measurements of the underlying quantum state. The measurements can be complicated. Quantum Chess algebraic notation does not provide a way to say what a measurement was, only that a measurement occurred and what its outcome was. This is done by appending `.m#` to the end of the move string, where `#` denotes the numbered outcome of the measurement.

* `b1wc3.m0` denotes an excluded move from b1 to c3, with measurement outcome 0.

Note: By convention, the `0` outcome of a measurement coincides with projecting the quantum state into a subspace where the subsequent movement operator is not performed.
