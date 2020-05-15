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
5. `message`, a raw bytestream of `mlen` bytes, only in some messages.

### C>S>C Asset request

A request is sent from an agent to the place, which may then continue sending it to another agent.

```
<<mid:1>><<hlen>>{
  "id": "<<asset id>>",
  "published_by": "<<entity id>>" // optional
}
```

* `id` is the ID of the asset
* `published_by` is the entity ID whose owner is likely to own the asset. This
  is an optional field and only used as a hint.
  Placeserv should ask this agent first, before asking other agents.

### C>S>C Asset response, success header

```
<<mid:2>><<hlen>>{
  "id": "<<asset id>>",
  "cache_until": <<server time as epoch since 1970>>,
  "chunk_count": <<total number of chunks in response>>",
  "chunk_length": <<total number of bytes in each chunk>>",
  "cache_options": ["nocache"], // optional
  "total_length": <<total byte length of response>>,
}
```


### C>S>C Asset response, success chunk

```
<<mid:3>><<hlen>>{
  "id": "<<asset id>>",
  "chunk_id": <<which chunk in series this is, 0-indexed>>,
}\n<<raw data>>
```

#### C>S>C Asset response, failure header

```
<<mid:4>><<hlen>>{
  "id": "<<asset id>>",
  "error_reason": "<<user-readable unavailability reason>>",
  "error_code": "<<computer-reladable error code for this error>>",
}<<mlen bytes of raw data>>
```

