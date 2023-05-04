---
layout: post
title:  "Event Sourcing"
date:   2023-04-27
---

## WIP

## What is Event Sourcing

`Git` for data

```javascript
function sayHello(name) {
  if (!name) {
    console.log('Hello World');
  } else {
    console.log(`Hello ${name}`);
  }
}
```

## Glossary

* Command - Request for change
* Event - Something that happnend
* Aggregate/Projection - Result of all events

## Pros

* Event driven - Decouples consumers from producer
* Can replay events so see how system got into that state

## Cons

* How big is an Event?
  * `UserUpdatedQuestion` - What if you only need to update the title of a question?
  * `UserUpdatedQuestionTitle` - Are we going to create a Handler for each property?
  * `UserUpdatedProperty` - ?Harder to make sense of?
  * `QuestionTitleChanged` - ?Was the event caused by a user?

## Investigate

* Race conditions
  * Has user voted on the comment? -> No -> Create `UserVotedOnComment` event
    * As Event sourcing is eventually consistent there will be a peroid
      where the predicate is still true until the event is processed so
      more of the same event could be added to the queue

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

* https://vvvvalvalval.github.io/posts/2018-11-12-datomic-event-sourcing-without-the-hassle.html
