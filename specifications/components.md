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

Defines the visual geometry of an entity

```
"geometry": {
  "type": "tristrip",
  "data": [1.0, 2.0, 3.0, 4.0, 5.0]
}
```

```
"geometry": {
  "type": "hardcoded-model",
  "name": "cubegal"
}
```
