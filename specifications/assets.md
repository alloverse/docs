# Asset protocol

Assets are delivered between agents by using the placeserv as a distribution center. 
Channel 2 `CHANNEL_ASSETS` is used to deliver reliable messages in any direction.
Messages can be requests, responses, informational or raw asset data.

"Assets" in this context means static, unchanging data used by clients or other agents,
such as textures, 3d models, sound files, etc. Basically, anything that would be
distributed as resource files in an app or a game.

There are no provisions for editing files, or keeping track of changes. While the
asset system could be used to distribute arbitrary file systems; editing, listing
and change notifications would have to be implemented as interactions.

## Identity

Assets are always identified with the format:

   `asset:sha256:<<64 lowercase hex digits>>`
 
The hex digits are the sha-2 256 bit hash of the full file contents of the asset itself.
This means that you can't have different versions of the same asset on different clients,
because then they are in effect different assets.

No filename, mime type or any other metadata is provided in the identifier. If this turns
out to be essential, we could add it as additional fields, separating fields with colons.

## Distribution

In general, the distribution protocol uses "pull" semantics from agents that need data.
When an agent requests an asset from placeserv, it will first try to serve it from cache
to the requesting agent. If none is found, it broadcasts a request to all connected agents
to find who has it. Placeserv then retrieves the asset to placeserv's cache, and then serves
the asset back to the requesting agent. 

The requesting agent might already have a good idea about who has the asset, and MAY include 
an entity id as a hint to which client SHOULD be asked first (whereafter it MUST ask all
other agents to see if they have it).

In addition, if an agent has an asset that it thinks is very likely to be needed soon, it
MAY push that asset immediately to the placeserv to be stored in the cache.

Once an asset is received, it MUST be checked against the hash in its identifier to verify
its integrity, and discard it if the hash does not match.

## Caching

When an agent publishes an asset, it MUST also include an expiration date in server time.
When this date is passed, servers and agents SHOULD purge it from its caches. Agents MUST
not use expired assets, and servers MUST not forward expired assets.

Placeserv SHOULD keep a per-agent maximum cache size, and a total maximum cache size.
When cache is nearing full, it may start removing cached assets to free up space.

Agents MAY indicate that an asset is not cacheable.

Placeserv MUST function even completely without a cache. In the case of an asset response
which doesn't fit on disk, or an asset response which indicates not cacheable, placeserv 
must stream this asset directly to the requesting agent, keeping just enough of it in RAM 
to do so, whereafter it can remove the asset from memory.

## Wire protocol

On the wire, messages are:

1. `mid`, a 16-bit unsigned big-endian integer message type code.
2. `hlen`, a 16-bit unsigned big-endian integer header length value.
4. `header`, a utf8 json body of `hlen` bytes.
5. `message`, a raw bytestream of bytes, directly appended after `hlen` bytes
   of json and until the end of the packet.

### C>S>C Asset request

Ask for an asset. The message can be:

* agent to placeserv: Ask the place for an asset, in which case it will either
  respond with it from cache; or ask other agents for the asset and then forward
  it on.
* placeserv to agent: Placeserv has been asked by another agent for an asset, and
  is now forwarding that request.

```
<<mid:1>><<hlen>>{
  "id": "<<asset id>>",
  "range": [start_byte_offset, number_of_bytes],
  "published_by": "<<entity id>>" // optional
}
```

* `id` is the ID of the asset
* `range` is the byte range you want. Send `[0, 0]` for a head request.
* `published_by` is the entity ID whose owner is likely to own the asset. This
  is an optional field and only used as a hint.
  Placeserv should ask this agent first, before asking other agents.

### C>S>C Asset response, transmission header

If the receiver has the asset requested, it may respond affirmative with the
metadata of the asset in a "transmission header" packet. It will then commence sending
"transmission chunks".

An agent can start sending this to a placeserv (or placeserv to an agent) unprompted
in case it wishes to warm up the receiver's cache.

Once we receive `start_byte_offset + number_of_bytes == total_length` the full asset has been received.
The receiver should start confirming the hash of the asset before
finally storing it in its cache and forwarding it to the application layer.


```
<<mid:2>><<hlen>>{
  "id": "<<asset id>>",
  "range": [start_byte_offset, number_of_bytes],
  "total_length": <<total byte length of asset>>,
}<<raw data>>
```

### C>S>C Asset response, failure header

Sent in place of `mid:2` in case the receiver is unable to satisfy the
request (e g it doesn't have the asset).

```
<<mid:3>><<hlen>>{
  "id": "<<asset id>>",
  "error_reason": "<<user-readable unavailability reason>>",
  "error_code": "<<computer-reladable error code for this error>>",
}
```

