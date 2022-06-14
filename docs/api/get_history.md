# qchess_get_history
Get the move history for a given game as an array of [qchess_Moves](./Move.md).

## Syntax
```cpp
int qchess_get_history(qchess_Game* game, qchess_Move* out_buffer, size_t buffer_size, size_t* out_size);
```

## Parameters
### ```game```
Pointer to game on which to perform the move

### ```out_buffer```
Pre-allocated array of qchess_Moves to be filled with the move history for the game.

### ```buffer_size```
Number of qchess_Moves ``out_buffer``` was allocated to hold.

### ```out_size```
Required number of qchess_Moves for the output buffer.

## Return value
Returns 0 if successful, or 1 if ```out_size > buffer_size```

## Example
```cpp
auto game = qchess_new_game("position startpos", false, false, "");
qchess_GameData data;
int out_value;
auto success = qchess_do_move(game, "b1^a3c3", &data, &out_value);

qchess_Move history[10];
size_t required_size
auto success = qchess_get_history(game, &history, 10, &required_size);
```
