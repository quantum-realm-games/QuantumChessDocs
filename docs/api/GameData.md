# GameData struct
C-style structure containing information about the current game state.

## Syntax
```c
typedef struct {
        char    pieces[64];
        float   probabilities[64];
        int     ply;
        int     fifty_count;
        int     castle_flags;
        int     ep_square;
    } qchess_GameData;
```

## Members

### ```pieces```
64-character array (UTF-8) corresponding to the types of pieces occupying each of the squares.
Piece characters are given in [FEN notation](https://en.wikipedia.org/wiki/Forsyth%E2%80%93Edwards_Notation).
Square indices are mapped as follows.
<p align="center">
   <img src="../images/chess_board_indices.png" width="300" height="300">
</p>

### ```probabilities```
64-float array of the probability that a given square is occupied.

### ```ply```
[Ply](https://en.wikipedia.org/wiki/Ply_(game_theory)) count for the game.

### ```fifty_count```
Number of ply since the last attempted capture or pawn move.

### ```castle_flags```
Bit flags indicating castling legality. In little endian notation:
```
bit 0 = white kingside castle
bit 1 = black kingside castle
bit 2 = white queenside castle
bit 3 = black queenside castle
```
Example: ```castle_flags = 3``` indicates white and black can both castle kingside.

### ```ep_square```
Square that can be captured by en passant.
Example: If white moves a2a4, ```ep_square``` is set to 24.
