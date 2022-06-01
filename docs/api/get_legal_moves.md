# qchess_get_legal_moves
Get the legal moves for a given game as an array of [Moves](./Move.md).

## Syntax
```cpp
int qchess_get_legal_moves(Game* game, Move* out_buffer, size_t buffer_size, size_t* out_size);
```

## Parameters
### ```game```
Pointer to game on which to perform the move

### ```out_buffer```
Pre-allocated array of Moves to be filled with the legal moves for the game.

### ```buffer_size```
Number of Moves ``out_buffer``` was allocated to hold.

### ```out_size```
Required number of Moves for the output buffer.

## Return value
Returns 0 if successful, or 1 if ```out_size > buffer_size```

## Example
```cpp
auto game = qchess_new_game("position startpos", false, false, "");

Move moves[24];
size_t required_size;
auto success = legal_moves(game, &moves, 24, &required_size);
```
