# qchess_undo_move
Undo the last move that was successfully executed for a game created by [qchess_new_game](./qchess_new_game.md)
## Syntax
```cpp
int qchess_undo_move(Game* game, GameData* out_data);
```
## Parameters
### ```game```
Pointer to the game to be deleted

### ```out_data```
Pointer to the GameData to be filled with the new game state data after successful undo.

## Return value
Returns 0 if successful, otherwise some yet to be defined status code.

## Examples
```cpp
auto game = qchess_new_game("position startpos", true, true, "");
GameData data;
int out_value;
auto success = qchess_do_move(game, "b1^a3c3", &data, &out_value);
success = qchess_undo_move(game, &data);
```
