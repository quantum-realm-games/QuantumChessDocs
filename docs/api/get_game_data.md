# qchess_get_game_data
Fill a buffer with data for the current game state.
## Syntax
```cpp
int qchess_get_game_data(Game* game, GameData* out_buffer);
```
## Parameters
### ```game```
Pointer to game on which to perform the move

### ```out_buffer```
Output [GameData](./GameData.md) buffer that will be filled with the GameData state information.

## Return value
Returns 0 if move was successful, otherwise some yet to be defined status code.

## Examples
```cpp
auto game = qchess_new_game("position startpos", false, false, "");
GameData data;
auto success = qchess_get_game_data(game, &data);
```

