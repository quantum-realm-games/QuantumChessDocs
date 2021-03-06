# qchess_do_move
Attempt to execute a move on a game created by the [qchess_new_game](./new_game.md) function.
## Syntax
```cpp
int qchess_do_move(qchess_Game* game, const char* move, qchess_GameData* out_buffer, int* move_code);
```
## Parameters
### ```game```
Pointer to game on which to perform the move

### ```move```
String defining the move in [Quantum Chess algebraic notation](../qc_algebraic_notation.md)

### ```out_buffer``` (optional)
Output [qchess_GameData](./GameData.md) buffer that will be filled with the post move state information.

### ```move_code```
Status code for the move, intended to give relevant information about the game after move execution. Possible values:
```
0 = FAIL (unspecified failure to execute move on current state)
1 = SUCCESS (successful move, no more information)
2 = WHITE_WIN (black has no king)
3 = BLACK_WIN (white has no king)
4 = MUTUAL_WIN (neither player has a king)
5 = DRAW (game has ended in a draw by some means)
```

## Return value
Returns 0 if move was successful (codes 1-5) , otherwise some yet to be defined status code.

## Examples
```cpp
auto game = qchess_new_game("position startpos", false, false, "");
qchess_GameData data;
int out_value;
auto success = qchess_do_move(game, "b1^a3c3", &data, &out_value);
```

