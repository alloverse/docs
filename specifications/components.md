# Official components

For an overview of what a component is, please see [specifications](README.md).

These interactions are defined by alloserv. Third party developers may
create any components they want. They can vote to make their own
components official by opening an issue on this repo.

## `transform`

Defines the physical location of the entity.

```
"transform": {
  "position":	[0, 0, 0],
  "rotation":	[0, 0, 0]
}
```

## `geometry`

Defines the visual geometry of an entity. This should use assets in the future,
but until we have assets, geometry is encoded in-line in entity description.

**If type is `hardcoded-model`**, you're using one of the models hard-coded
into the visor. `name` is the name of the model, `color` is the rgba value
to color the model.

```
"geometry": {
  "type": "hardcoded-model",
  "name": "cubegal",
  "color": [1.0, 0.0, 0.0, 1.0]
}
```

**If type is `inline`**, you're defining geometry inline in the component.
This is only recommended for debugging, and until we have geometry assets.

* `vertices` is a required list of lists, each sub-list containing x, y, z coordinates for your vertices.
* `normals` is an optional list of lists, each sub-list containing the x, y, z coords for the normal at the corresponding vertex.
* `uvs` is an optional list of lists, each sub-list containing the u, v texture coordinates at the corresponding vertex.
* `triangles` is a required list of lists. Each sub-list is three integers, which are indices into the above arrays, forming a triangle.
* `texture` is optional texture data as base64encoded png.

```
"geometry": {
  "type": "inline",
  "vertices": [[1.0, 1.0, 1.0], [2.0, 2.0, 2.0], [3.0, 3.0, 3.0], [4.0, 4.0, 4.0]],
  "normals": [[1.0, 1.0, 1.0], [2.0, 2.0, 2.0], [3.0, 3.0, 3.0], [4.0, 4.0, 4.0]],
  "uvs": [[0.0, 0.0], [0.5, 1.0], [1.0, 0.0], [1.0, 1.0]],
  "triangles": [[0, 1, 2], [1, 2, 3]]
  "texture": "iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAAAXNSR0IArs4c6QAAAAlwSFlzAAAOxAAADsQBlSsOGwAAAVlpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IlhNUCBDb3JlIDUuNC4wIj4KICAgPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4KICAgICAgPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIKICAgICAgICAgICAgeG1sbnM6dGlmZj0iaHR0cDovL25zLmFkb2JlLmNvbS90aWZmLzEuMC8iPgogICAgICAgICA8dGlmZjpPcmllbnRhdGlvbj4xPC90aWZmOk9yaWVudGF0aW9uPgogICAgICA8L3JkZjpEZXNjcmlwdGlvbj4KICAgPC9yZGY6UkRGPgo8L3g6eG1wbWV0YT4KTMInWQAAAttJREFUOBFtU0tsjFEUPvfx//O3TJ+mbToe1RSRSJQfRYqJZFTSxkYqsbBgMUQ0jddCBLMRQbGxaVhgIRGWFiIaDYsmfaALbUiakopH+phW6Mz8/3049zczunCSm3PuOd+553kBchSLJXleNlxrTSD2kidjwAM5Z9TJGG8HYAuxNJbUBef6tpvH6luvHVkIMPKZjXWHDzRUteT1gwnXQpmQvAL5Drej71bFyq0uKAW/Jgb6+obfnDrVezwbb2++3VgT3j6X8eDtt9knB58OnUf8R+NrHihbd+jRjSVrWo4UlZWCEuArTFBlwZob6IL4zBXoWL0NFtvaz2rCihilo1Nz6tXnySune0cvk8bE83e1bny9TINUGnTGk5x9eQ3R711+JJy25lk1ZFJT/n6WtjZVONqxbaEBeNjm5PHIxAvulC6txmgghQJveoxHPt2BGvoQ7PItlq/C2kFjqKLcepBeBIM/Zsm+sLCWhh0/K5UVLSleRrXy0xRTznhZEh27ACuKe4AtioOQHKj2iCKUgJRQ53D4VFkFd1NaCyEIVgKekFnTfQtTwrkpsIqqQJEMaKyHEKwHqLEEvfawvlIMFApZROrAAzHEooSwDCiDQqXykePB4fyFGP0/kgaCDxkyDUNvoCKdcngxAGW2h1ngU2YwCDJRCifwCSySUIVRRdixIC1lhAkpRuzi+s28pLZmSaqHOHTSl9rBHFRhRzBVs5kaGPMd4fGtjuL9X1Mf7r8ZP5EHkVD8+sk9of7OhlqxXJMQ9k346GO2zVQkKGWUcU6nP0/M14y/v3R1ZKYrsLlutzU0dNQUb4g1N204W7cscrE6UlmE3VYYV1g2t3/+/A0TX77de9Y7cA5x3w242w3W2YhA3MRgEC24AUT37m56kDi0T3cm2vX+tl3DqN+Zs0F37h/k7ws5Sfw1BrpV9dHWJnft8TwgiT8R5dxs89r/c5YDF6yxWOBcuOeFP4paJwh7m0NPAAAAAElFTkSuQmCC"
}
```

**If type is `asset`**, well... you're living in the future.

## `relationships`

Specify the relationships between entities, in particular child entites' "parent" entity. If an entity has a parent, its transform should be concatenated with all its ancestors' transforms before being displayed.


```
"relationships": {
  "parent": "abc123"
}
```

## `poses`

Specify how an entity's transform should be set from its owning client's intent.

```
"poses": {
  "inherit": "hand/left"
}
```
