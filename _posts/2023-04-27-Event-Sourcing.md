---
layout: post
title:  "WIP - Event Sourcing"
date:   2023-04-27
---

## What is Event Sourcing?

> **TL;DR** `Event sourcing` is basically `Git` for data

With `event sourcing` you store each `event` (state transition) in an `event log` so you have auditability
just like git so...

* **Latest**
  * Using the `event log` apply each event to generate the `projection` (state)
* **Check previous states**
  * Only apply a certain amount of `events` in the `event log` so that you can see the `projection` at a certain time
* **Experiment applying new events**
  * What would the `projection` look like if I applied this `event`?
* **Bisect**
  * Run through applying `events` until condition met

Whereas with a traditional system we overwrite the state in place (PLOP) losing
each state transition so we have no idea how the state ended up in that state.

## Glossary

* Event - Something that happnend
* Event log - Append-only log
* Projection - Result of events
* Stream - Related events

## Example

```sql
# Event stored in a DB

CREATE TABLE (
    id uuid primary key     # Unique id for event
    stream_id uuid          # The aggregate the event blongs too
    type varchar(200)       # Event type name
    data jsonb              # Additional data include with the event
)
```

```clojure
(defn find-events
    "Find all events releated to thing in database"
    [database thing-id])

(defn projection
  "Apply events to create projection"
  [events]
  (reduce apply-event events))

(defn get-latest-projection
    "Helper function to fetch and apply events"
    [database thing-id]
    (let [events (find-events database thing-id)
          projection (projection events)]
        projection))

(defn add-event
    "Adds event to events table"
    [tx event])

(defn update-projection
    "Update projection"
    [tx thing-id]
    (db/update-record (get-latest-projection tx thing-id)))

(defn command
    "Preforms side-effect and trys to the log event within a transaction"
    [database projection total-service thing-id]
    (jdbc/with-db-transaction [tx database]
        (let [thing (get-latest-projection database thing-id)]
              total-response (total-service/get-total total-service thing)]
            (when (:error total-response)
                (throw "exception fetching total"))
            (add-event tx (thing-events/thing-event thing-id))
            (update-projection tx thing-id))))
```

## Pros

It creates an **Audit Trail** so you can see how your system got into
its current state making it easier to **Debug** and reason about.

* **Replayability** - Extract all `events` related to an `aggregate` from the
`event log` and replay them in a test enviorment so you can inspect each state transision
* **Bisect** - Apply each event until condition met
* **Experement** - What would the projection look like if you applied this event?

## Cons

* How big is an Event?
  * `UserUpdatedQuestion` - What if you only need to update the title of a question?
  * `UserUpdatedQuestionTitle` - Are we going to create a Handler for each property?

## Eventual Consistency

Should the projection update immediately?

Apply the event then

* Yes, Update the projection in one transaction
* No, Do nothing

## Versioning

What happens if you need to retroactively update an event type.
Well there are two ways for managing versioning.

* Create a new event `fix-previous-event` but where in your Domain model
does this event exist?
* Event versioning

## Resources

* [(PLOP) The Value of Values - Rich Hickey](https://www.youtube.com/watch?v=-6BsiVyC1kM&ab_channel=InfoQ)
* <https://vvvvalvalval.github.io/posts/2018-11-12-datomic-event-sourcing-without-the-hassle.html>
