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

See [intent.md](intent.md) for JSON payload.

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

## C->S->C Media track packet


* Channel: 3 ("CHANNEL_MEDIA")
* Kind: Unreliable
* Related interaction: [allocate_track](https://github.com/alloverse/docs/blob/master/specifications/interactions.md#entity_wishes_to_transmit_live_media)


```
<<track-id as 32bit big endian integer>><<raw media payload>>\n
```

Yeah that's right. Even audio packets have
that stupid newline at the end.

## Unspecified

* C->S asset request/response
* S->C asset request/response
* S->C asset force delivery
