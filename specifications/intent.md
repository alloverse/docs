# Intent

Sent from agent to place over unreliable channel 0 every heartbeat
to indicate movement of clients. This intent is then actuated
onto the client state both by client-side interpolation
and on the server at the server heart rate.

Format of the packet on-wire today:

```
{
  "cmd": "intent",
  "intent": {
    "zmovement": 0, // 1 = maximum speed forwards
    "xmovement": 0, // 1 = maximum speed strafe right
    "yaw": 0, // absolute rotation around x in radians
    "pitch": 0, // absolute rotation around y in radians
    "poses": {
      "head": {
        "matrix": [m11, m12, ...m44],
      },
      "hand/left": {
        "matrix": [m11, m12, ...m44],
        "skeleton": [
          [m11, m12, ...m44], 
          [m11, m12, ...m44],
          ...
        ],
        "grab": { // nil or description of grab
          //entity id of entity being grabbed
          "entity": "asdf", 
          // to keep hand-to-entity spatial relationship constant during grab
          "grabber_from_entity_transform": [m11, m12, ...m44]
        }
      },
      "hand/right": {same as hand/left}
    },
    "ack_state_rev": 1234
  }
}
```

For the format of the matrix in `poses.*.matrix`, [see coordinate-system.md](coordinate-system.md).

For `hand*.skeleton`: It's an array of matrices, each describing the pose of a node of the hand.
There are 26 nodes, with each index as defined by OpenXR hand tracking. You can see a list of
indexes (and node parents) in [lovr's documentation](https://lovr.org/docs/lovr.headset.getSkeleton)
(with each index offset by 1 because lua, of course).

To understand `ack_state_rev`, see (state.md)[state.md].

Planned version of the packet:

```
{
    "intents": {
        "{entity id of avatar}": {
            (same as under "intent" in today's version)
        },
        "{id of other owned entity}": {
            // same as above
        }
    },
    "ack-state-rev": 73892  // sequence number of state diff
                            // we're acknowledging
}
```
