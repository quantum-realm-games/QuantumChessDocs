# get_pairwise_bell_measures
Fill a buffer with data for the [Bell correlations](../math.md#bell-correlations) of a given pair of squares, with the following index mapping
<p align="center">
   <img src="../images/chess_board_indices.png" width="60%">
</p>

## Syntax
```cpp
long get_pairwise_bell_measures(Game* game, int square1, int square2, GameData* out_buffer, int buffer_size);
```
## Parameters
### ```game```
Pointer to game on which to calculate the correlation values.

### ```square1```
Integer index of the first square for the correlation calculation.

### ```square2```
Integer index of the second square for the correlation calculation.

### ```out_buffer```
8-float array that will be filled with the expectation values of the [Bell correlation operators](../math.md#bell-correlations), ordered O<sub>1</sub> at index 0 to O<sub>8</sub> at index 7, for the given squares.

## Return value
Returns 0 if successful, otherwise some yet to be defined status code.

## Examples
```cpp
auto game = new_game("position startpos", false, false, "");
GameData data;
int out_value;
auto success = do_move(game, "b1^a3c3", &data, &out_value);
if(success==0){
    float correlations[8];
    success = get_pairwise_bell_measures(game, 8, 10, &correlations);
}
```

