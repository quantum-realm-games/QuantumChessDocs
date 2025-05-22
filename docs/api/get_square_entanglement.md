# qchess_get_square_entanglement
For each square on the board, get the pairwise entanglement of that square with a user-specified
square. 0 is given for the entanglement of a square with itself. Log negativity is used as the
entanglement measure for a pair of square qubits.

## Syntax
```cpp
int qchess_get_square_entanglement(qchess_Game* game, int square, qchess_SquareEntanglementData* out_data);
```

## Parameters
### `game`
Quantum chess game.

### `square`
Index of the square to find pairwise entanglements for.

### `out_data`
Receives the entanglement results. The structure looks like
```cpp
struct qchess_SquareEntanglementData {
    float pairwise_log_negativity[64];
};
```

## Return value
Returns 0 if successful or nonzero on error.
