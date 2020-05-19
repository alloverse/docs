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
        "matrix": [1.0,0.0,0.0,0.0, 0.0,1.0,0.0,0.0, 0.0,0.0,1.0,0.0, 0.0,0.0,0.0,1.0]
      },
      "hand/left": {same as head},
      "hand/right": {same as head}
    }
  }
}
```

For the format of the matrix in `poses.*.matrix`, [see coordinate-system.md](coordinate-system.md).

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
