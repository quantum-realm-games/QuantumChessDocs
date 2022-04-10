# delete_game
Deletes a pointer to a Quantum Chess game created by the [new_game](./new_game.md).
## Syntax
```cpp
long delete_game(Game* game);
```
## Parameters
### ```game```
Pointer to the game to be deleted.

## Return value
Returns 0 if successful, otherwise some yet to be defined status code.

## Examples
 ```cpp
 auto game = new_game("position startpos", true, true, "");
 
 auto code = delete_game(game);
 ```
