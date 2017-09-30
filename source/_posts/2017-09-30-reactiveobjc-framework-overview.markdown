---
layout: post
title: "ReactiveObjC Framework Overview"
date: 2017-09-30 15:04:20 +0800
comments: true
categories: ReactiveObjC
keywords: sxgfxm, ReactiveObjC Tutorial, ReactiveObjC Documents
description: ReactiveObjC Tutorial, ReactiveObjC Documents
---

# Streams

A **stream**, represented by the **RACStream** abstract class, is any series of object values.

## Signals
A **signal**, represented by the **RACSignal** class, is a push-driven stream.  

Signals generally represent data that will be delivered **in the future**. Users must **subscribe** to a signal in order to access its values.  

Signals send three different types of events to their subscribers:  

1. The **next** event provides a new value from the stream.
2. The **error** event indicates that an error occurred before the signal could finish.
3. The completed event indicates that the signal finished successfully, and that no more values will be added to the stream. 

<!-- more -->

### Subscription

A subscriber is anything that is waiting or capable of waiting for events from a signal.  
A subscription is created through any call to `-subscribeNext:error:completed:`, or one of the corresponding convenience methods.

### Subjects
A **subject**, represented by the **RACSubject** class, is a signal that can be manually controlled.  
Subjects are extremely useful for bridging non-RAC code into the world of signals.

### Commands
A **command**, represented by the **RACCommand** class, creates and subscribes to a signal in response to some action.  
This makes it easy to perform side-effecting work as the user interacts with the app.

### Connections
A **connection**, represented by the **RACMulticastConnection** class, is a subscription that is shared between any number of subscribers.  
A connection is created through the `-publish` or `-multicast:` methods on RACSignal, and ensures that only one underlying subscription is created, no matter how many times the connection is subscribed to.

## Sequences
A **sequence**, represented by the **RACSequence** class, is a pull-driven stream.  
Sequences are a kind of collection, sequences cannot contain `nil`.

## Disposables
The **RACDisposable** class is used for cancellation and resource cleanup.

## Schedulers
A **scheduler**, represented by the **RACScheduler** class, is a serial execution queue for signals to perform work or deliver their results upon.  
Schedulers are similar to Grand Central Dispatch queues, but schedulers support cancellation (via disposables), and always execute serially.  
RACScheduler is also somewhat similar to NSOperationQueue, but schedulers do not allow tasks to be reordered or depend on one another.

## Value types
RAC offers a few miscellaneous classes for conveniently representing values in a stream:  

1. **RACTuple** is a small, constant-sized collection that can contain nil (represented by RACTupleNil). It is generally used to represent the combined values of multiple streams. 
2. **RACUnit** is a singleton "empty" value. It is used as a value in a stream for those times when more meaningful data doesn't exist.  
3. **RACEvent** represents any signal event as a single value. It is primarily used by the -materialize method of RACSignal.  