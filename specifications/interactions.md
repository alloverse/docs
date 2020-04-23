# Official interactions

For an overview of what an interaction is, please see [README.md](README.md).

These interactions are defined by alloserv. Third party developers may
create any interactions they want. They can vote to make their own
interactions official by opening an issue on this repo.

## Agent announce

After an agent connects, before it can interact with the place
it must announce itself and spawn its avatar entity. Failure to
announce will lead to force disconnect.

* Receiver: `place`
* Type: `request`
* Request body:

```
[
  "announce",
  "version",
  1,
  "identity",
  {
    // identity body goes here
  },
  "spawn_avatar",
  {
    // same as "spawn_entity" key in "Agent requests to spawn entity"
  }
]
```

* Response:

```
[
  "announce",
  "{ID of avatar entity}",
  "{name of place}"
]
```

## Agent requests to spawn entity

* Receiver: `place`
* Type: `request`
* Request body:

```
[
  "spawn_entity",
  {
    // list of initial values for components for new entity goes here
    
    "children": [
      // list of new child entities to create; same body as for "spawn_entity".
      // These will automatically get a "relationships" component set up referencing
      // the parent entity.
      { 
        // list of initial values for components for child of new entity goes here
      },
      ...
    ]
  }
]
```

* Response:

```
[
  "spawn_entity",
  "{ID of entity if spawned}"
]
```


## Agent requests to change/add/remove component(s) in entity

* Receiver: `place`
* Type: `request`
* Request body:

```
[
  "change_components",
  "{entity ID}",
  "add_or_change",
  {
    // object with new values for components (regardless of if
    // component already exists on entity)
  }
  "remove",
  [
    // keys of components to remove
  ]
]
```

Response:

```
[
  "change_components",
  "ok"
]
```

A default ACL rule is set so that you must own the entity
whose component you're changing, but this rule can be changed.

## Subscribe/unsubscribe

* Receiver: `place`
* Type: `request`
* Request body for subscribe:

```
[
  "subscribe",
  """{guard pattern}"""
]
```

* Response:

```
[
  "subscribe",
  "{subscription ID}"
]
```

* Unsubscribe request body:

```
[
  "unsubscribe",
  "{subscription ID}"
]
```

* Response:

```
[
  "unsubscribe",
  "{'ok'|'not_subscribed'}"
]
```

* Example:

```
  [
    "interaction",
    "request",
    "1234",
    "place", // place is the subscription gateway
    "567",
    [
      "subscribe",
      """[
        "new_tweet",
        TweetSender,
        TweetBody
      ] where TweetSender == "nevyn" """
    ] 
```

In this scenario the place contains an app that publishes new tweets
as interactions to the room. This interaction will ask the place to
subscribe to all interaction publications which start with the
word "new_tweet" followed by two fields. The guard clause asks that
the sender must be "nevyn". If the guard matches, the publication
interaction will be forwarded by the place from the sending agent
to the subscribing agent.

## Modify ACL

## Entity points at another entity

An entity, most likely the avatar of a user, is pointing with their
finger at another entity. This is used as a precursor to actually
interacting with it ("poking it").

The interaction describes two points in world space: the tip of
the finger, and the intersection point between the ray from the
finger and the nearest entity, so that the entity can know _where_
on itself someone is pointing.

* Receiver: The pointed-at entity
* Type: `one-way`
* Body:

```
[
  "point",
  [1.0, 2.0, 3.0], // finger tip in world space coordinates
  [4.0, 5.0, 6.0], // intersection point in world space coordinates
]
```

If the ray cast from the user's finger veers off the last pointed-at
entity, one last message is sent to indicate that the user has stopped
pointing at it. This is useful for removing highlight effect, etc.

```
[
  "point-exit"
]
```

## Entity pokes

Once an entity is pointing at another entity, it can ask to "physically"
interact with it, by turning the pointing into a poke. The poke doesn't
contain vector information -- it's up to the receiver to correlate with
pointing events, as those will be streaming a continuously updating
location, while poking is a request-response which the receiver
can reject. Such a rejection should be visualized, so that the
sender's user can understand if and why poking failed.

* Receiver: The pointed-at entity
* Type: `request`
* Request body:

```
[
  "poke",
  {true|false} // whether poking started (true) or stopped (false)
]
```

* Success response: 

```
[
  "poke",
  "ok"
]
```

* Failure response:

```
[
  "poke",
  "failed",
  "{string explaining why, presentable to user}"
]
```

## Entity wishes to transmit live media

Before an entity can transmit streamed audio, video or geometry, a track must be created
along which to send that data. This interaction will add a 
[live_media](https://github.com/alloverse/docs/blob/master/specifications/components.md#live_media)
component to the sender's entity.

Only audio, and only mono opus, is supported right now.

* Receiver: `place`
* Type: `request`
* Request body:

```
[
  "allocate_track",
  "audio", # media type
  48000, # sample rate
  1, # channel count
  "opus" # media format
]
```

* Success response: 

```
[
  "allocate_track",
  "ok",
  3 # track_id
]
```

* Failure response:

```
[
  "allocate_track",
  "failed",
  "{string explaining why, presentable to user}"
]
```

