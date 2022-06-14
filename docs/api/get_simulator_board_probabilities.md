# qchess_get_simulator_board_probabilities
For a simulator board, get the exact probability of each bitboard in the superposition.

The number of bitboards is at least 1 and at most the number of states given by
qchess_get_simulator_state_size().

## Syntax
```cpp
int qchess_get_simulator_board_probabilities(qchess_Game* game, qchess_SampleData* out_buffer, size_t buffer_len, size_t* out_len);
```

## Parameters
### ```game```
Pointer to game.

### ```out_buffer```
Receives the bitboard/probability data.

### ```buffer_len```
Length of `out_buffer`.

### ```out_len```
Receives the number of bitboards written to `out_buffer`.

## Return value
Returns 0 if successful.
Returns nonzero if the board is not simulator-based or the user-provided buffer is too small.
