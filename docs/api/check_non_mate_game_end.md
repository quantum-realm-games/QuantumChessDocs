# qchess_check_non_mate_game_end
Check what happens if the 50-move rule is claimed.

According to the rules of quantum chess, if a successful claim is made, then all squares on the board are
measured, and a final result is determined from the resulting classical position. This API assists in the
implementation of that rule. But note that this API does NOT affect the state of the game object in any
way; the measurement is only notional.

If no game end condition can be claimed:
- `*conditions` is 0
- `*move_code` is `QCHESS_MOVECODE_SUCCESS`
- `*position` is not defined

If at least one game end condition can be claimed:
- `*conditions` is a mask of applicable `enum qchess_GameEndCondition`s
- `*move_code` is DRAW, WHITE_WIN, BLACK_WIN, or MUTUAL_WIN
- `*position` is the bitboard resulting from the "measurement"

## Syntax
```cpp
int qchess_check_non_mate_game_end(qchess_Game* game, int* conditions, int* move_code, uint64_t* position);
```

## Parameters
### ```game```
Pointer to game.

### ```conditions``` (optional)
Receives the applicable game end conditions.

### ```move_code``` (optional)
Receives the game result (if it can be ended), or otherwise SUCCESS

### ```position``` (optional)
Receives the final position after "measuring" the board, if applicable

## Return value
Returns 0 if successful.
