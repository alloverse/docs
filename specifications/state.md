# State diffs

Every server heart-beat, the server sends the world state to all connected agents
on unreliable channel 0. 

## State representation

The world representation is stored as a big JSON document like so:

```
{
  "entities": {
    "abc123": {
      "id": "abc123",
      "components": { ... }
    }
  },
  "revision": 1234
}
```

* `entities`: A dictionary from entity ID to [entity description](https://github.com/alloverse/docs/tree/master/specifications#entity).
* `revision`: An integer ID for this specific revision of the world. Monotonically 
  increases until it reaches the biggest sequential integer that a 64-bit IEEE 
  double can represent, which is 2^53, whereafter it will wrap around back to 0.
  (damn JSON).

## Deltas

Sending this document in its entirety every server heart-beat would be needlessly wasteful,
so instead only the difference to a previous known-good state is sent. An agent acknowledges
successful receival of world state by setting `ack_state_rev` in [`intent`](intent.md) to
the received world state, whereafter the server will diff against that revision instead.
Note that it takes time for this ACK to be received, so you might continue to receive
diffs against an older version; so you need to keep a history of previous states to apply
diffs to.

The packet on channel 0 received in the client can take three forms:

### Format 1: `set`

If the server is unable to diff, it will just send the full world state document.

```
{
  "patch_style": "set",
  "entities": ...,
  "revision": 1234
}
```

Note that `patch_style` is not part of the document, and should be removed before
the document is stored to history.

### Format 2: `apply`

Indicates that the payload is an [RFC 6902](https://tools.ietf.org/html/rfc6902)
JSON Patch.


```
{
  "patch_style": "apply",
  "patch_from": 1233,
  "patches": [ ... /*RFC 6902 patches*/ ]
}
```

* `patch_from` indicates the revision to apply the patches to
* `patches` is a JSON list of RFC 6902 patches to apply to said revision
  from history

The patches should be applied and the resulting document stored to history. If
the referenced `from` revision is not available or history, or if patching fails,
you should set intent's `ack_state_rev` back to 0 to request a full `set` state.

**Note**: This format seems to almost always be less efficient than RFC 7386,
so its support is likely to be removed.

### Format 3: `merge`

Indicates that the payload is an [RFC 7386](https://tools.ietf.org/html/rfc7396)
JSON Merge Patch.


```
{
  "patch_style": "merge",
  "patch_from": 1233,
  ... // rest of document is an RFC 7386 payload
}
```

* Note that `patch_style` and `patch_from` must be removed from the document before
  it's a valid RFC 7386 merge patch.
* `patch_from` indicates the revision to apply the patches to

The merge patch should be applied and the resulting document stored to history. If
the referenced `from` revision is not available or history, or if patching fails,
you should set intent's `ack_state_rev` back to 0 to request a full `set` state.


# Older versions

Version 1 sent the complete world-state as is, and with entities as an array rather
than an object. It looked like this:

```
entities: [
  // list of all entities; see Entities above for structure
],
revision: 1234 // monotonically increasing integer (will roll over to 0 after INT64_MAX!)
```
