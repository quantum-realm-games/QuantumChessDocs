# qchess_new_game
Returns a pointer to a new Quantum Chess game
## Syntax
```cpp
Game* qchess_new_game(char* position, bool force_turn, bool force_win, char* rest_url);
```
## Parameters
### ```position```
String defining the starting position of the new game. Position strings look like 
```cpp
"position startpos (optional)moves [move strings]"
```
to initialize the game to a standard chess starting position, or
```cpp
"position fen [fen string] (optional)moves [move strings]"
```
to initialize to some other customized starting position given in [FEN notation](https://en.wikipedia.org/wiki/Forsyth%E2%80%93Edwards_Notation).
Move strings are given in [Quantum Chess Algebraic Notation](../qc_algebraic_notation.md)

### ```force_turn```
Bool to enable/disable turn order checking

### ```force_win```
Bool to enable/disable win condition checking

### ```rest_url```
String for URL to offload quantum state to. If this parameter is empty, the engine uses its internal simulator. 
If the string is not empty, the engine assumes it is a valid URL, that implements the [Quantum Chess REST API](../qc_rest_api.md),
and will offload the quantum state to this external resource. This feature requires special build flags to use.

## Return value
Pointer to newly created game if successfull, otherwise nullptr.

## Examples
 ```cpp
 auto position = "position startpos";
 auto game = qchess_new_game(position, false, false, "");
 ```
 ```cpp
 auto position = "position fen 4k3/8/8/8/8/8/8/4K3 w - - 0 1 moves e1^e2d1 e8^d8f8";
 auto game = qchess_new_game(position, false, false, "");
 ```
