---
layout: post
title:  "WIP - Event Sourcing"
date:   2023-04-27
---

## What is Event Sourcing?

"`Git` for data"

A traditional system would overwrite a value in place (PLOP).
`Event sourcing` instead records each event then reduces over the `Event log`
leading to the latest state `projection` just like bank account transactions
or commits for Git

## Glossary

* Event - Something that happnend
* Event log - Append-only log
* Projection - Result of events
* Stream - Related events

## Example

**Event** - Store each event in a DB row

* id - Unique id for event
* stream-id - Link related events together
* version - ?
* type - Event type
* data - Any additional data added to the event

```sql
CREATE TABLE (
    id uuid primary key
    stream_id uuid
    version int
    type varchar(200)
    data jsonb
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

* Audit Trail - How did your system got into it's current state
* Debugging - Extract events from log and replay them in a test enviorment

## Cons

* How big is an Event?
  * `UserUpdatedQuestion` - What if you only need to update the title of a question?
  * `UserUpdatedQuestionTitle` - Are we going to create a Handler for each property?

## Datomic

`[entity attribute value transcation added]` (datom)

* Entity - Unquie identifier for an entity
* Attribute - Property assoicated with entity
* Value - Value of the property
* Transaction - When we learned the fact
* Added - Are we adding or retracting the fact

* Datomic is basically just a list of Datoms (Over simplified)

* Transaction - Command (Attempt to append new facts)
* Transcations - Facts that have been applied
* Datomic - Event log + Aggregate

### Datomic - Pros

* Datoms are fine grain no guessing of Event size
* No need to re-process event log
  * `(d/as-of db #inst "2017-12-25")` - Can view database as of a date
  * `(d/tx-range (d/log conn) t0 t1)` - Can view transactions between date range

## Resources

* [(PLOP) The Value of Values - Rich Hickey](https://www.youtube.com/watch?v=-6BsiVyC1kM&ab_channel=InfoQ)
* <https://vvvvalvalval.github.io/posts/2018-11-12-datomic-event-sourcing-without-the-hassle.html>
