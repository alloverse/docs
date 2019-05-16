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

If type is `tristrip`, you're defining a triangle strip.
* "tris" will be x, y, z coordinates for one point in the tristrip each.
* "normals" is optional
* "uvs" are u, v coords, so list count should be divisible by 2 rather than 3. also optional
* "texture" is texture data, as png, base64encoded so we can stick it in json. also optional.
* "color": rgba value (as four floats 0-1) for all verts, optional
* "colors": rgba value for each vert, optional

```
"geometry": {
  "type": "tristrip",
  "tris": [1.0, 1.0, 1.0, 2.0, 2.0, 2.0, 3.0, 3.0, 3.0],
  "normals": [1.0, 1.0, 1.0, 2.0, 2.0, 2.0, 3.0, 3.0, 3.0],
  "uvs": [1.0, 1.0, 2.0, 2.0, 3.0, 3.0],
  "colors": [1.0, 0.0, 0.0, 1.0, 1.0, 1.0, 0.0, 1.0, 1.0, 1.0, 1.0, 1.0],
  "texture": "base64encoded png"
}
```

If type is `hardcoded-model`, you're using one of the models hard-coded
into the visor. `name` is the name of the model, `color` is the rgba value
to color the model.

```
"geometry": {
  "type": "hardcoded-model",
  "name": "cubegal",
  "color": [1.0, 0.0, 0.0, 1.0]
}
```
