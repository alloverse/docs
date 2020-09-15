# Official components

For an overview of what a component is, please see [specifications](README.md).

These interactions are defined by alloserv. Third party developers may
create any components they want. They can vote to make their own
components official by opening an issue on this repo.

## `transform`

Defines the physical location and orientation of the entity relative to its parent
entity if it has one; otherwise relative to the world origo.
[See coordinate-system.md](coordinate-system.md) for an extended description of how
things are positioned and oriented in Alloverse.

```
"transform": {
  "matrix": [1.0,0.0,0.0,0.0, 0.0,1.0,0.0,0.0, 0.0,0.0,1.0,0.0, 0.0,0.0,0.0,1.0]
}
```

_note: In an early version of the protocol, transform was represented as a 3-element position vector
and 3-element rotation vector with euler angle rotations._

## `geometry`

Defines the visual geometry of an entity. This should use assets in the future,
but until we have assets, geometry is encoded in-line in entity description.

**If type is `hardcoded-model`**, you're using one of the models hard-coded
into the visor. `name` is the name of the model.

```
"geometry": {
  "type": "hardcoded-model",
  "name": "hand"
}
```

**If type is `inline`**, you're defining geometry inline in the component.
This is only recommended for debugging, and until we have geometry assets.

* `vertices` is a required list of lists, each sub-list containing x, y, z coordinates for your vertices.
* `normals` is an optional list of lists, each sub-list containing the x, y, z coords for the normal at the corresponding vertex.
* `uvs` is an optional list of lists, each sub-list containing the u, v texture coordinates at the corresponding vertex.
* `triangles` is a required list of lists. Each sub-list is three integers, which are indices into the above arrays, forming a triangle.

```
"geometry": {
  "type": "inline",
  "vertices": [[1.0, 1.0, 1.0], [2.0, 2.0, 2.0], [3.0, 3.0, 3.0], [4.0, 4.0, 4.0]],
  "normals": [[1.0, 1.0, 1.0], [2.0, 2.0, 2.0], [3.0, 3.0, 3.0], [4.0, 4.0, 4.0]],
  "uvs": [[0.0, 0.0], [0.5, 1.0], [1.0, 0.0], [1.0, 1.0]],
  "triangles": [[0, 1, 2], [1, 2, 3]]
}
```

`geometry` used to also contain `texture`, but that's been moved to `material.texture`.

**If type is `asset`**, well... you're living in the future.

## `material`

Defines the surface appearance of the component being rendered.

```
"material": {
  "color": [1.0, 1.0, 0.0, 1.0],
  "shader_name": "plain",
  "texture": "iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAAAXNSR0IArs4c6QAAAAlwSFlzAAAOxAAADsQBlSsOGwAAAVlpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IlhNUCBDb3JlIDUuNC4wIj4KICAgPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4KICAgICAgPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIKICAgICAgICAgICAgeG1sbnM6dGlmZj0iaHR0cDovL25zLmFkb2JlLmNvbS90aWZmLzEuMC8iPgogICAgICAgICA8dGlmZjpPcmllbnRhdGlvbj4xPC90aWZmOk9yaWVudGF0aW9uPgogICAgICA8L3JkZjpEZXNjcmlwdGlvbj4KICAgPC9yZGY6UkRGPgo8L3g6eG1wbWV0YT4KTMInWQAAAttJREFUOBFtU0tsjFEUPvfx//O3TJ+mbToe1RSRSJQfRYqJZFTSxkYqsbBgMUQ0jddCBLMRQbGxaVhgIRGWFiIaDYsmfaALbUiakopH+phW6Mz8/3049zczunCSm3PuOd+553kBchSLJXleNlxrTSD2kidjwAM5Z9TJGG8HYAuxNJbUBef6tpvH6luvHVkIMPKZjXWHDzRUteT1gwnXQpmQvAL5Drej71bFyq0uKAW/Jgb6+obfnDrVezwbb2++3VgT3j6X8eDtt9knB58OnUf8R+NrHihbd+jRjSVrWo4UlZWCEuArTFBlwZob6IL4zBXoWL0NFtvaz2rCihilo1Nz6tXnySune0cvk8bE83e1bny9TINUGnTGk5x9eQ3R711+JJy25lk1ZFJT/n6WtjZVONqxbaEBeNjm5PHIxAvulC6txmgghQJveoxHPt2BGvoQ7PItlq/C2kFjqKLcepBeBIM/Zsm+sLCWhh0/K5UVLSleRrXy0xRTznhZEh27ACuKe4AtioOQHKj2iCKUgJRQ53D4VFkFd1NaCyEIVgKekFnTfQtTwrkpsIqqQJEMaKyHEKwHqLEEvfawvlIMFApZROrAAzHEooSwDCiDQqXykePB4fyFGP0/kgaCDxkyDUNvoCKdcngxAGW2h1ngU2YwCDJRCifwCSySUIVRRdixIC1lhAkpRuzi+s28pLZmSaqHOHTSl9rBHFRhRzBVs5kaGPMd4fGtjuL9X1Mf7r8ZP5EHkVD8+sk9of7OhlqxXJMQ9k346GO2zVQkKGWUcU6nP0/M14y/v3R1ZKYrsLlutzU0dNQUb4g1N204W7cscrE6UlmE3VYYV1g2t3/+/A0TX77de9Y7cA5x3w242w3W2YhA3MRgEC24AUT37m56kDi0T3cm2vX+tl3DqN+Zs0F37h/k7ws5Sfw1BrpV9dHWJnft8TwgiT8R5dxs89r/c5YDF6yxWOBcuOeFP4paJwh7m0NPAAAAAElFTkSuQmCC"
}
```

* `color`: An array of R, G, B and A value to set as base color for the thing being rendered. Default is white.
* `shader_name`: Optional ame of hard-coded shader to use for this object. Currently allows `plain` and `pbr`. Default is `plain`.
* `texture`: optional texture data as base64encoded png. Default is none.

## `text`

Defines a text renderer for this entity, drawing a text texture at `transform`.

```
"text": {
  "string": "hello world",
  "height": 0.03,
  "wrap": 0,
  "halign": "center"
}
```

* `height`: The height of the text. 1 unit = 1 meter.
* `wrap`: The width in meters at which to wrap the text, if at all.
* `halign`: Horizontal alignment; "center", "left" or "right".

## `collider`

Defines the physical shape of an entity.

```
"collider": {
  "type": "box",
  "width": 1,
  "height": 1,
  "depth": 1
}
```

## `relationships`

Specify the relationships between entities, in particular child entites' "parent" entity. If an entity has a parent, its transform should be concatenated with all its ancestors' transforms before being displayed.


```
"relationships": {
  "parent": "abc123"
}
```

## `intent`

Specify how the entity's owning agent's intent affects this entity.

* `actuate_pose`: this named pose will be set as this entity's transform each frame.
```
"intent": {
  "actuate_pose": "hand/left"
}
```

## `grabbable`

Describes how an entity maybe grabbed/held, and then
moved/dragged by a user.

The actual grabbing is accomplished using intents.
See the field `grab` under [intent](intent.md).

```
"grabbable": {
  "actuate_on": "...",

  "translation_constraint": [1, 1, 1],
  "rotation_constraint": [1, 1, 1],
}
```

* **`actuate_on`**: Since the `grabbable` component is likely attached
  to a a handle rather than the entire object being movable,
  actuate_on indicates how far up this entity's ancestry to walk before deciding which entity to actually move.
    * Omitting this key indicates the entity itself should be 
      moved within its local coordinate space.
    * Literal `$parent` means move the parent entity
    * Any entity ID must be an ancestor of this entity, and
      indicates exactly which entity to move.
* **`translation_constraint`**: Only allow the indicated fraction of movement in
  the corresponding axis in the actuated entity's local coordinate space. E g, to only
  allow movement along the floor (no lifting), set the y fraction 
  to 0: `"translation_constraint": [1, 0, 1]".
* **`rotation_constraint`**: Similarly, constrain rotation to the given fraction
  in the given euler axis in the actuated entity's local coordinate space. E g, to only allow
  rotation along Y (so that it always stays up-right),
  use: `"rotation_constraint": [0, 1, 0]`.

## `live-media`

The entity that holds a `live-media` component for a specific track
is the entity that "plays" that track; e g for audio, audio will be played
from the location of that entity.

Please do not try to create live-media components manually. They must be
allocated server-side so that the server can allocate a track stream in
the network protocol. Instead, send
[allocate_track](https://github.com/alloverse/docs/blob/master/specifications/interactions.md#entity_wishes_to_transmit_live_media)
to `place` to add a `live-media` component to your entity.


* `track_id`: `CHANNEL_MEDIA` track number that corresponds to what this
  entity should play back
* `sample_rate`: For audio: playback sample rate, as sent in `allocate_track`
* `channel_count`: For audio: only 1 mono channel supported
* `format`: Only "opus" audio supported

```
"live_media": {
  "track_id": 0, // filled in by server
  "sample_rate": 48000, // 
  "channel_count": 1,
  "format": "opus"
}
```
