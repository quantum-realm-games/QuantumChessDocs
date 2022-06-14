# qchess_Move struct

## Syntax
```
	struct Move {
		using Square = Board::Square;

		enum class Type : std::uint8_t {
			NULL_TYPE = 0,
			JUMP = 2,
			SLIDE = 3,
			SPLIT_JUMP = 4,
			SPLIT_SLIDE = 5,
			MERGE_JUMP = 6,
			MERGE_SLIDE = 7,
			PAWN_CAPTURE = 10,
			PAWN_EP = 11,
			KS_CASTLE = 12,
			QS_CASTLE = 13,
		};

		enum class Variant : std::uint8_t {
			UNSPECIFIED,
			BASIC,
			EXCLUDED,
			CAPTURE
		};

		uint8_t _reserved;
		Square square1 = Square::NONE;
		Square square2 = Square::NONE;
		Square square3 = Square::NONE;
		Type type = Type::NULL_TYPE;
		Variant variant = Variant::UNSPECIFIED;
		bool does_measurement = false;
		std::uint8_t measurement_outcome = 0;
		Piece promotion_piece = Pieces::NO_PIECE;

	};
  ```
## Enums
### ```Type```
8-bit integer denoting the type of move.

### ```Variant```
8-bit integer denoting the move variant. See [Move Variants](../rules.md#move-variants).

## Members

### ```square1```
8-bit integer index for the first square involved in a move. This is the source, or first source for merge moves.

### ```square2```
8-bit integer index for the second square involved in a move. This is the target, or second source for merge moves.

### ```square3```
(Optional) 8-bit integer index for the third square involved in a move. This is the second target for split moves, or sole target for merge moves.

### ```type```
The 8-bit ```Type``` of the move.

### ```variant```
The 8-bit ```Variant``` of the move.

### ```does_measurement```
Bool specifying whether a move has a predetermined measurement outcome. This is generally seen when repeating move histories.
Example: ```a2xa4.m0``` specifies a capturing move from a2 to a4 with predetermined measurement outcome 0.

### ```measurement_outcome```
8-bit integer denoting the predetermined measurement outcome associated with ```does_measurement = true```.

### ```promotion_piece```
8-bit integer denoting the type and color of piece to promote to in case of a pawn promotion.
Values:
```
2 = black knight, 3 = black bishop, 4 = black rook, 5 = black queen, 10 = white knight, 11 = white bishop, 12 = white rook, 13 = white queen
```
