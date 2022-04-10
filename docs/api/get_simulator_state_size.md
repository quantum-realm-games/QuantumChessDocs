# get_simulator_state_size
Get the number of quantum states in the superposition, based on the quantum simulator.

The number of states may be larger than the number of possible bitboards due to states
differing only in ancilla qubits.

## Syntax
```cpp
long get_simulator_state_size(Game* game, int64_t* out_size);
```

## Parameters
### ```game```
Pointer to game.

### ```out_size```
Receives the number of states.

## Return value
Returns 0 if successful.
