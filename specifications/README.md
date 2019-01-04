# Terminology

Please begin by reading the [architecture documentation](../architecture),
as it explains how all these components fit together.

## Agent

An agent is code that can spawn entities, and communicates to a place
on behalf of the entities it owns. Thus, all these are agents:

* The place itself (referenced in requests with a null/empty string entity id)
* any visor (represented as the allonet connection to the place)
* any app (also represented as its allonet connection)

Every connected agent has to spawn its "avatar" entity as part of its `ANNOUNCE`
message, so that it has a representation in a room and can be communicated with
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
    "displayName": "annie"
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

# Types of communication

## Entity intent

Sent from agent to place over unreliable channel every heartbeat.

```
{
    "intents": {
        "entityId1 (avatar)": {
            "zmovement": 1, // movement "into the screen", meters per sec
            "xmovement": 0, // left/right movement
            "yaw": 0, // radians of absolute yaw rotation
            "pitch": 0 // radians of absolute pitch rotation
        },
        "id of other owned entity": {
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

JSON TBD


## Place to agent state update

Sent every heart-beat on the unreliable channel to let agents
know what the world looks like. In v1, the full place state
is sent every heartbeat.
In v2, a diff from the previously acknowledged state will be sent.

JSON TBD

# Official interactions

## Agent announce

## Agent requests to spawn entity

## Entity points

## Entity pokes

# http endpoints

## Alloapp Gateway

HTTP service that takes a connection request, repackages as env
vars, and boots the requested process. Node express.

* `ALLOVERSE_PLACE_URL`
* `ALLOVERSE_REQUESTER_IDENTITY`
* `ALLOVERSE_URL_PARAMS`


