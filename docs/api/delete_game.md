# qchess_delete_game
Deletes a pointer to a Quantum Chess game created by the [qchess_new_game](./new_game.md).
## Syntax
```cpp
int qchess_delete_game(qchess_Game* game);
```
## Parameters
### ```game``` (optional)
Pointer to the game to be deleted.

## Return value
Returns 0 if successful, otherwise some yet to be defined status code.

## Examples
 ```cpp
 auto game = qchess_new_game("position startpos", true, true, "");

 auto code = qchess_delete_game(game);
 ```
