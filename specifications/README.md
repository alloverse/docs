# Terminology

Please begin by reading the [architecture documentation](../architecture),
as it explains how all these components fit together.

To see how these messages actually sent over the network, see
[Wire protocol](wire-protocol.md). This document also specifies the differences
between the specification and the current implementation.

## Agent

An agent is code that can spawn entities, and communicates to a place
on behalf of the entities it owns. Thus, all these are agents:

* The place itself (referenced in requests with "place" magic entity id)
* any visor (represented as the allonet connection to the place)
* any app (also represented as its allonet connection)

Every connected agent has to spawn its "avatar" entity as part of its `ANNOUNCE`
message, so that it has a representation in a place and can be communicated with
(or terminated, in the case of apps).

When an agent disconnects from a place, all its entities are removed from the
place.

## Agent identity

An agent's `ANNOUNCE` message contains its identity: display name, profile URL,
and a certificate/public key that can be used to identify the same user/app
across connections and places without a central identity authority server.

Identity is currently expressed as the following JSON blob:

```
{
    "display_name": "annie"
}
```

## Entity

An entity is the manifestation of an agent in a place. The
only fixed information in an entity is its `id`. Everything
else is specified as a set of components.

JSON specification:

```
{
    "id": "1234",
    "components": {
        "transform": // every component has a string key
        {            // and an object value
            // the keys and values should be consistent with
            // with the specification for that key. See "Component"
        }
    }
}
```


## Component

A component describes an aspect of an entity, such as:

* `transform`: its position, rotation and size
* `mesh`: description of its physical geometry
* etc...

Alloverse defines **a set of [official component
specifications](components.md)**. App developers are free
to invent their own components, which is useful for
app-to-app communication (though a standard Visor will
not be able to interpret them).

## Intent

The "intent" blob is used every heart-beat to indicate
how the agent intends that its avatar (and other owned
entities) should move and behave in real-time this frame.
It only covers basic movement: more complex behavior is
covered by "interactions". It's sent over the unreliable
channel, so it should be repeated every frame as long as
it's relevant.

The blob is also used to send housekeeping information
such as acknowledging receipt of state diffs.

## Interaction

An on-demand message either announcing information, or
requesting something from one entity to another (sent by
the requesting entity's agent, and handled by the receiving
entity's agent).

Alloverse defines **a set of
[official interactions](interactions.md)**. App developers 
are free to invent their own interactions, which is useful for
app-to-app communication (though a standard Visor will
not be able to interpret them).

# Types of communication

## Entity intent

Sent from agent to place over unreliable channel every heartbeat.

```
{
    "intents": {
        "{entity id of avatar}": {
            "zmovement": 1, // 1 = maximum speed forwards
            "xmovement": 0, // 1 = maximum speed strafe right
            "yaw": 0, // absolute rotation around x in radians
            "pitch": 0 // absolute rotation around y in radians
            "poses": {
                "head": {
                    "position": [0, 0, 0], // relative to avatar center
                    "rotation": [0, 0, 0]  // euler angles around x, y, z for the device
                },
                "hand/left": {same as head},
                "hand/right": {same as head}
        },
        "{id of other owned entity}": {
            // same as above
        }
    },
    "ack-state-rev": 73892  // sequence number of state diff
                            // we're acknowledging
}
```


## Entity to entity interaction (request/response/pubsub)

Sent from agent, to place, then forwarded to the designated agent,
over the reliable channel on demand.

```
  [
    "interaction",
    "{oneway|request|response|publication}"
    "{source entity ID}",
    "{destination entity ID or emptystring if publication}",
    "{request ID or empty string if single-way}",
    // interaction body goes here
  ]
```

The agent sending the request must own the source entity.

### Requests and responses

If the second field is set to "request" and a request ID is set,
the recipient can respond to the interaction by setting "response"
and filling in the same request ID, and the alloplace server will route
the message correctly.

A response must be sent to the entity that sent the request. Sending
a response to a an entity that didn't request it may lead to 
force disconnection.

The client sets its own request IDs. These must be unique for
each request this session for this agent. An UUID or monotonically
increasing integer as string should do.

### Publication and subscription

If the second field is "publication" and no destination entity is
filled in, it's a publication that anyone can subscribe to. This is
useful for apps to broadcast information that is instantaneous
rather than a property of an entity (in which case it would have been
a property of a component of an entity).

Agents can subscribe to publications by providing a "match pattern"
which is an Elixir guard expression. This lets agents set
very fine grained subscriptions to exactly what interactions it
wants to receive.

If nobody is subscribing to a matching pattern of the given
publication, the message is filtered out by the alloplace server.

Behavior is undefined if you provide a request id in a publication.
Future versions may allow publications to have responses.

### Oneway

The message is directed to a single entity/client, and you can't respond
to it. `request_id` should be empty.

### Access control

Access control is defined on the interaction level. The alloplace server
can be fed with Elixir guard expressions the same way that
subscriptions work, and an "allow" or "deny" flag,
which then becomes the "Access Control List" (ACL) for interactions
in the place.

Since announce is an interaction, an interaction ACL can be used
to allow/deny agents from joining a place. Likewise, since changing
the ACL is also an interaction, you can use an ACL to control who
may modify the ACL. In other words, anything except intents and
place state updates can be filtered using interaction ACLs.

Interaction ACLs guard on the entire interaction message,
and not just the payload body. So for example, the following
rule would disallow users named "harry" from joining a room:

```
[
  "deny",
  """[
    "interaction", // match exactly on provided value for first field
    "request",
    SourceEntity,  // bare words become variables
    "place",
    _,             // underscore means unused variable = ignored value
    [
      "announce",
      ["identity", Identity],
      _
    ]
  ] where Identity["name"] == "harry" """
]
```

Note that the rule itself is an Elixir string, not a JSON
expression.

If an interaction is sent by an agent but is disallowed by an ACL
rule, the following message is sent in response:

```
[
  "interaction_denied",
  // original interaction goes here,
  // rule that caused denial goes here
]
```

## Place to agent state update

Sent every heart-beat on the unreliable channel to let agents
know what the world looks like. In v1, the full place state
is sent every heartbeat.
In v2, a diff from the previously acknowledged state will be sent.

```
  entities: [
    // list of all entities; see Entities above for structure
  ],
  revision: 1234 // monotonically increasing integer (will roll over to 0 after INT64_MAX!)
```

See [Entity](#entity) for definition of the entries of the `entities` list.

# Official components

Please see the [list of official components](components.md) in
a separate document. These are defined by alloplace,
and define how an agent and entity interact with a place.


# Official interactions

Please see the [list of official interactions](interactions.md) in
a separate document. These are defined by alloplace,
and define how an agent and entity interact with a place.

# HTTP Endpoints

## Alloapp Gateway

HTTP service that takes a connection request, repackages as env
vars, and boots the requested process. Node express.

* `ALLOVERSE_PLACE_URL`
* `ALLOVERSE_REQUESTER_IDENTITY`
* `ALLOVERSE_URL_PARAMS`


