# Official interactions

For an overview of what an interaction is, please see [README.md](README.md).

These interactions are defined by alloserv. Third party developers may
create any interactions they want. They can vote to make their own
interactions official by opening an issue on this repo.

## Agent announce

After an agent connects, before it can interact with the place
it must announce itself and spawn its avatar entity. Failure to
announce will lead to force disconnect.

An interaction should be sent to "place" with the following body:

```
[
  "announce",
  "identity",
  {
    // identity body goes here
  },
  "spawn_avatar",
  {
    // list of initial values for components for avatar entity goes here
  }
]
```

Response:

```
[
  "announce"
  "{ID of avatar entity}"
]
```

## Agent requests to spawn entity

```
[
  "spawn_entity",
  {
    // list of initial values for components for avatar entity goes here
  }
]
```

Response:

```
[
  "spawn_entity",
  "{ID of entity if spawned}"
]
```


## Agent requests to change/add/remove component(s) in entity


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

Subscribe:

```
[
  "subscribe",
  """{guard pattern}"""
]
```

Response:

```
[
  "subscribe",
  "{subscription ID}"
]
```

Unsubscribe:

```
[
  "unsubscribe",
  "{subscription ID}"
]
```

Response:

```
[
  "unsubscribe",
  "{'ok'|'not_subscribed'}"
]
```

Example:

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

## Entity points

## Entity pokes
