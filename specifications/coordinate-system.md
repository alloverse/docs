# Coordinate systems in Alloverse

The coordinate system in Alloverse has the following properties:

1. One unit is 1 meter
2. Coordinate system is [right-handed](https://www.evl.uic.edu/ralph/508S98/coordinates.html). This implies...
3. Negative X is left/west, positive X is right/east.
4. Negative Y is down, positive Y is up.
5. Negative Z is forward/north, positive Z is back/south.
6. Positive rotation is counterclockwise about the axis of rotation.
7. Y=0 is floor level in model space
8. For the neutral pose of models/entities, the geometry should face towards negative Z.

![Rotation about Y axis is counterclockwise](https://www.evl.uic.edu/ralph/508S98/gif/righty.gif)

## Representation

Transforms are always represented using a 16-element transformation matrix. Such a 4x4 matrix is
represented in data models with a column-major 16-element list of numbers.
