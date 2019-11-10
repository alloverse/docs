# Wire protocol

What are the actual bytes travelling on the wire? They all seem to be json payload followed by a newline character...

## S->C State stream

* Channel: 0 ("CHANNEL_STATEDIFFS")
* Kind: Unreliable
* Payload specification: [specifications.md](https://github.com/alloverse/docs/tree/master/specifications#place-to-agent-state-update)

```
<<JSON-payload>>\n
```

## C->S Intent

* Channel: 0 ("CHANNEL_STATEDIFFS")
* Kind: Unreliable
* Payload specification: [specifications.md](https://github.com/alloverse/docs/blob/master/specifications/README.md#entity-intent)

```
<<JSON-payload>>\n
```

Version 0 implements this packet as:

```
{
  "cmd": "intent",
  "intent": {
    "zmovement": 0,
    "xmovement": 0,
    "yaw": 0,
    "pitch": 0,
    "poses": {
      "head": {
          "position": {"x": 0, "y": 0, "z": 0}, // relative to avatar center
          "rotation": {"x": 0, "y": 0, "z": 0}  // euler angles around x, y, z for the device
      },
      "hand/left": {same as head},
      "hand/right": {same as head}
    }
  }
}
```

To make matters worse, it sends it on _channel 1_, as a _reliable_ message. WTF.

## S->C Interaction

* Channel: 1 ("CHANNEL_COMMANDS")
* Kind: Reliable
* Payload specification: [specifications.md](https://github.com/alloverse/docs/blob/master/specifications/README.md#entity-to-entity-interaction-requestresponsepubsub)

```
<<JSON-payload>>\n
```

Version 0 implements this packet as:

```
{
  "cmd": "interact",
  "interact": {
    "from_entity": "...",
    "to_entity": "...",
    "cmd": "..."
  }
}\n
```

## C->S Interaction


* Channel: 1 ("CHANNEL_COMMANDS")
* Kind: Reliable
* Payload specification: [specifications.md](https://github.com/alloverse/docs/blob/master/specifications/README.md#entity-to-entity-interaction-requestresponsepubsub)

```
<<JSON-payload>>\n
```

Version 0 does not implement this packet.

## Unspecified

* C->S asset request/response
* S->C asset request/response
* S->C asset force delivery
* Audio/video
