# Quantum Chess Engine API
This document defines a c-style API for interacting with the Quantum Chess Engine. 

# Structures
* [GameData](./api/GameData.md) : Information about the current state of a game.
* [Move](./api/Move.md) : Data used to completely describe a move.

# Functions
* [new_game](./api/new_game.md) : Create a new game.
* [delete_game](./api/delete_game.md) : Deallocate game memory.
* [do_move](./api/do_move.md) : Apply a move procedure to a game.
* [undo_move](./api/undo_move.md) : Undo the last move in a game.
* [get_game_data](./api/get_game_data.md) : Get [GameData](./api/GameData.md) for the current state of a game.
* [get_history](./api/get_history.md) : Get the move history for a game.
* [get_legal_moves](./api/get_legal_moves.md) : Get the legal moves for the current state of a game.
* [get_pairwise_bell_measures](./api/get_pairwise_bell_measures.md) : Get the [Bell correlation values](./math.md#bell-correlations) between two squares for the current state of a game.
* [get_simulator_state_size](./api/get_simulator_state_size.md) : Get simulated number of states in the game's superposition.
* [get_simulator_board_probabilities](./api/get_simulator_board_probabilities.md) : (Simulator only) Get the exact probability of each bitboard.
