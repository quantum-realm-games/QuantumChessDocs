# Overview
This document details the math underlying Quantum Chess. It provides a basic set of unitaries on which all moves are built, as well as quantum circuit diagrams utilizing those unitaries in movement procedures for each type and variant of move. Finally, it gives an example of how one might extract specific information from the quantum state, via Bell operator expectation values.

# State Representation
The state of the game is divided into two parts, the quantum state and classical information

## Quantum State
The quantum state is described using 64 qubits that encode the "occupancy" of each square. 
<p align="center">
   <img src="./images/quantum_state.png" >
</p>

## Classical Information
A classical register is mapped on top of the quantum "occupancy" state, to denote what kind of piece may be in a square.

# Movement Unitaries
All movement is accomplished by applying a specific unitary to the quantum state, and then updating the classical information to reflect the new possible piece locations. The movement unitaries in Quantum Chess are built around the iswap and square root of the iswap.
<p align="center">
   <img src="./images/iswap.png" width="240" height="120"> <img src="./images/root_iswap.png" width="240" height="120">
</p>

The exact matrix form of each movement unitary will depend on the qubit ordering on which it acts. Movement unitaries are defined as acting on sources, targets, and path qubits:
* **source** refers to a square you're moving from
* **target** refers to a square you're moving to
* **path** refers to an ancilla qubit that encodes the occupancy of all squares between a source and a target. A path is clear if all squares in the path are unoccupied. A path is blocked if any square in the path is occupied. A superposition of boards can lead to a path qubit in a superposition of being clear and blocked.


These terms are for convenience, and are defined under the assumption that there is no superposition. Movement unitaries can be thought of as sometimes moving a piece "backwards", from target to source, when superposition is involved.

All move procedures in the game use one or more of the following movement unitaries.

## Jump
The jump is simply the iswap unitary applied to two qubits.
* s : source
* t : target
<p align="center">
   <img src="./images/jump.png">
</p>

## Slide
The slide acts on three qubits.
* s : source
* t : target
* p : path between source and target

It is a zero-controlled jump between s and t, with p acting as control.
<p align="center">
   <img src="./images/slide.png">
</p>

## Split Jump
The split jump acts on three qubits.
* s : source
* t<sub>1</sub> : first target
* t<sub>2</sub> : second target

It applies the [square root of iswap](#movement-unitaries) between s and t<sub>1</sub>, followed by an [iswap](#movement-unitaries) between s and t<sub>2</sub>.
<p align="center">
   <img src="./images/split_jump.png">
</p>

## Split Slide
The split slide acts on five qubits.
* s : source
* t<sub>1</sub> : first target
* t<sub>2</sub> : second target
* p<sub>1</sub> : path from s to t<sub>1</sub>
* p<sub>2</sub> : path from s to t<sub>2</sub>

It is a sequence of controlled unitaries. If both p<sub>1</sub> and p<sub>2</sub> are clear, it performs the [split jump](#split-jump) between s, t<sub>1</sub> and t<sub>2</sub>. If p<sub>1(2)</sub> is blocked, it performs a [jump](#jump) between s and t<sub>2(1)</sub>. If both paths are blocked, it does nothing.
<p align="center">
   <img src="./images/split_slide.png">
</p>

## Merge Jump
The merge jump acts on three qubits.
* s<sub>1</sub> : first source
* s<sub>2</sub> : second source
* t : target

It is the inverse of a [split jump](#split-jump), and here the qubits are ordered to reflect the inversion.
<p align="center">
   <img src="./images/merge_jump.png">
</p>

## Merge Slide
The merge jump acts on five qubits.
* s<sub>1</sub> : first source
* s<sub>2</sub> : second source
* t : target
* p<sub>1</sub> : path from s<sub>1</sub> to t
* p<sub>2</sub> : path from s<sub>2</sub> to t

It is the inverse of a [split jump](#split-jump), and here the qubits are ordered to reflect the inversion.
<p align="center">
   <img src="./images/merge_slide.png">
</p>

# Measurement
Measurement is used to enforce the "No Double Occupancy" rule. Measurements are designed to allow the game engine to unambiguously determine what type of piece might be occupying any given square after a move. 

All measurements in the game are two-outcome projective measurements, m<sub>0</sub> and m<sub>1</sub>. Each measurement outcome is designed with the following criteria.

* m<sub>0</sub> : The state is projected into a subspace where the proposed move is illegal on all boards in the superposition. No further action should be taken.

* m<sub>1</sub> : The state is projected into a subspace where the proposed move can be completed, and the piece information for the target square(s) can be updated to match the piece being moved (source piece).

The excluded and capture [variants](./rules.md#move-variants) of each type of move require measurement. The specific form each measurement takes can be seen in [Move Circuits](#move-circuits)

# Move Circuits
The movement unitaries and measurement criteria can be combined into quantum computing circuits for each type and variant of move. These move circuits define the procedure followed for each kind of move.

## Path Control

This circuit can be used to initialize such a [path control qubit](#movement-unitaries) for a path with 3 squares, and is generalizable to any number of squares.

<p align="center">
   <img src="./images/path_circuit.png">
</p>

The path will be considered unoccupied, p = 0, only if *all* squares in the path are unoccupied.


## Basic Jump
The basic jump is just a simple iswap between source and target.

<p align="center">
   <img src="./images/jump_circuit.png">
</p>

## Excluded Jump
The excluded jump requires measuring to determine the occupancy of the target. The [basic jump](#basic-jump) is then applied if the outcome is m<sub>1</sub>, meaning the target is unoccupied. Otherwise no unitary evolution occurs.

<p align="center">
   <img src="./images/measure_excluded_jump_circuit.png">
</p>

## Capture Jump
An ancilla qubit must be added to the quantum state in order to perform capture in a unitary way. The piece to be captured is first swapped out into the ancilla space. Then a [basic jump](#basic-jump) between source and target is applied.

<p align="center">
   <img src="./images/capture_jump_circuit.png">
</p>

The capture jump requires a measurement to determine the occupancy of the source. The above circuit is applied if the outcome is m<sub>1</sub>, meaning the source is occupied. Otherwise no unitary evolution occurs.

<p align="center">
   <img src="./images/measure_capture_jump_circuit.png">
</p>

## Basic Slide
The basic slide is a zero-controlled [basic jump](#basic-jump), with the path qubit acting as the control, as described in [Slide](#slide)
<p align="center">
   <img src="./images/slide_circuit.png">
</p>

## Excluded Slide
The excluded slide requires measuring to determine the occupancy of the target. The [basic slide](#basic-slide) circuit is applied if the outcome is m<sub>1</sub>, meaning the target is unoccupied. Otherwise no unitary evolution occurs.

<p align="center">
   <img src="./images/measure_excluded_slide_circuit.png">
</p>

## Capture Slide
The capture slide requires simultaneously measuring to determine the occupancy of both the source and the path. The [capture jump](#capture-jump) circuit is appliedif the outcome is m<sub>1</sub>, meaning the source square is occupied and the path is clear. Otherwise no unitary evolution occurs.

<p align="center">
   <img src="./images/measure_capture_slide_circuit.png">
</p>

Note, if the measurement outcome is 0, both source and path may still exist in superposition.

## Basic Split Jump
The basic split jump is accomplished with a circuit corresponding to the math described in [Split Jump](#split-jump)

<p align="center">
   <img src="./images/split_jump_circuit.png">
</p>

## Basic Split Slide
The basic split slide is accomplished with a circuit corresponding to the math described in [Split Slide](#split-slide)

<p align="center">
   <img src="./images/split_slide_circuit.png">
</p>

## Basic Merge Jump
The basic merge jump is accomplished with a circuit corresponding to the math described in [Merge Jump](#merge-jump)

<p align="center">
   <img src="./images/merge_jump_circuit.png">
</p>

## Basic Merge Slide
The basic merge slide is accomplished with a circuit corresponding to the math described in [Merge Slide](#merge-slide)

<p align="center">
   <img src="./images/merge_slide_circuit.png">
</p>

## Basic Pawn Step
The basic pawn step uses the same circuit as the [basic jump](#basic-jump).

## Excluded Pawn Step
The excluded pawn step uses the same circuit as the [excluded jump](#excluded-jump).

## Basic Pawn Two Step
The basic pawn two step uses the same circuit as the [basic slide](#basic-slide).

## Excluded Pawn Two Step
The excluded pawn two step uses the same circuit as the [excluded slide](#excluded-slide).

## Pawn Capture
Pawn capture is similar to the capture jump, except the actual movement of the pawn location is dependent on whether there is is a piece being captured. This can be accomplished with a simple CNOT from target to source, after measurement of the source qubit. Add a phase rotation to maintain consistency with using iSwaps into a captured ancilla.

<p align="center">
   <img src="./images/pawn_capture_circuit_alt.png">
</p>
The capture variant requires a measurement projecting the source into the "occupied" subspace.

<p align="center">
   <img src="./images/measure_pawn_capture_circuit.png">
</p>

## Basic En Passant
Basic en passant is where the target is either unoccupied, or occupied by a pawn that is the same color as the pawn being moved. Name the square of the pawn being captured 'ep'. The following circuit can be used to complete the basic en passant procedure.

<p align="center">
   <img src="./images/ep_circuit.png">
</p>

## Excluded En Passant
Excluded en passant is when the target is occupied, in superposition, by a non-capturable piece. It requires the target square to be project into the "unoccupied" subspace.

<p align="center">
   <img src="./images/measure_excluded_ep_circuit.png">
</p>

We know the target is not occupied, so the following circuit can be used to complete the en passant procedure.
<p align="center">
   <img src="./images/excluded_ep_circuit.png">
</p>

## Capture En Passant
Capture en passant is when the target is occupied, in superposition, by a capturable piece. It requires a measurement projecting the source into the "occupied" subspace.

<p align="center">
   <img src="./images/measure_capture_ep_circuit.png">
</p>

In this case, the pawn is either capturing the piece occupying the target, or capturing the pawn via en passant. We know the source is occupied, so the following circuit can be used to complete the capture en passant procedure.

<p align="center">
   <img src="./images/capture_ep_circuit.png">
</p>

## Basic Kingside Castle
The squares involved in the kingside castle are labeled by their file. It is accomplished by an iswap to move the king, and an iswap to move the rook.

<p align="center">
   <img src="./images/ks_castle_circuit.png">
</p>

## Excluded Kingside Castle
The excluded variant of the kingside castle requires measuring to project the f-file and g-file squares into the simultaneously unoccupied subspace.

<p align="center">
   <img src="./images/measure_excluded_ks_castle_circuit.png">
</p>

## Basic Queenside Castle
The squares involved in the queenside castle are labeled by their file. It is accomplished by an zero-controlled-iswap to move the king, and an zero-controlled-iswap to move the rook, with the b-file square acting as the control qubit.

<p align="center">
   <img src="./images/qs_castle_circuit.png">
</p>

## Excluded Queenside Castle
The excluded variant of the queenside castle requires measuring to project the c-file and d-file squares into the simultaneously unoccupied subspace.

<p align="center">
   <img src="./images/measure_excluded_qs_castle_circuit.png">
</p>

# Bell Correlations
The Bell states, shown here, can be used to extract some correlation information from the quantum state

<p align="center">
   <img src="./images/bell_states.png">
</p>

The expectation values of the following operators, for given pairs of squares (qubits) in the quantum state, can be used to gain some sense of entanglement and relative phase.

<p align="center">
   <img src="./images/bell_operators.png">
</p>

## Entanglement
The Bell states can be separated by their "orientation" of entanglement. The Ψ states support a sense of "oppositeness", if the first square is occupied, then the second is not, and vice versa. Similarly, the Φ states support a sense of "sameness". 

Values for "same" and "opposite" correlation can be calculated by vectorizing the expectation values for the associated Bell operators. The magnitude of each subset of values will tell you whether the state has some preference for sameness or oppositeness. This measure includes both entanglement and classical correlation. The values can be normalized to give some ratio of sameness vs. oppositeness compared to the whole.

<p align="center">
   <img src="./images/entanglement.png">
</p>

## Phase
Relative phase between two squares can have many interpretions. Consider the relevant information when intepreting the expectation values of the above operators. 

The relative phases for the "opposite" Bell states will tell the player whether a merge move will act as expected. The expectation values of O<sub>1</sub>, O<sub>2</sub>, O<sub>3</sub>, and O<sub>4</sub> can be taken directly to show the "amount" of the state that is oriented in each of the 4 "opposite" rotations.
