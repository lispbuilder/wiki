#Describes the graphics capabilities of LISPBUILDER-SDL

# Types #

| `RECTANGLE` | Describes an area of width and height with the top left corner of the rectangle located at position x and y. |
|:------------|:-------------------------------------------------------------------------------------------------------------|
| `POINT`     | Represents an x and y location.                                                                              |
| [SURFACE](SURFACE) | Represents a drawable object.                                                                                |
| `FONT`      | A font.                                                                                                      |
| `COLOR`     | Contains RGB or RGBA color information.                                                                      |

# Functions/Methods/Macros #

| Function | Args | Description |
|:---------|:-----|:------------|
| `WITH-POINT` |      |
| `POINT`  |      |
| `COPY-POINT` |      |
| `X`      | `Object` | Returns the X position of the `Object`. Applies to [POINT](POINT), [RECTANGLE](RECTANGLE), [SURFACE](SURFACE), [FONT](FONT). |
| `SETF X` | `Object` |
| `Y`      |      |
| `SETF Y` |      |
| `POINT-*` |      |
| `GET-POINT` |      |
| `SET-POINT` |      |
| `SET-POINT-*` |      |
| `POSITION` |      |
| `SET-POSITION` |      |
| `SET-POSITION-*` |      |
