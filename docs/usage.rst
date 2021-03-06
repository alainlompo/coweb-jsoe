
======================
Usage and Requirements
======================

Overview
========

This document describes how to use the OT library to build correct collaborative
application.

Requirements
============

This OT library provides only the
`operational transform`_ algorithms and exposure to the engine. Communication
between remote clients is not provided by this library. Thus, the application
programmer is responsible for the communication mechanism (eg. hosting a central
server to ferry data between clients).

Example code is provided that creates a Node.js
server for remote clients to communicate and exchange data, but this is purely
for demonstration purposes.

Site Ids
~~~~~~~~

All participating peers must have a unique *site Id* assigned to them. This site
Id must be an integer value in the range [0, 2^32-1] that can safely be
represented by all undering agents. The safest and easiest way to assign site
Ids is with a central server. It is recommended that a counter be used to assign
increasing Ids starting from zero.

Total order
~~~~~~~~~~~

The underlying engine algorithm requires that all operations be totally ordered.
The simplest way to achieve this is for a central entity to arbitrarily provide
this. For example, the OpenCoweb project has a central server append a unique
integer to each operation before it sends the operations to other peers.

No two operations should ever be considered *equal*. Even if two operations
match identically, they should be assigned unique orders. Thus, the total order
requirement imposed by the OT engine is a little different from the set theory
total order, since no two elements may be equal.

For optimal engine performance, the total order should be *close* to the
temporal order that peers generate operations.

Syncing engine state
~~~~~~~~~~~~~~~~~~~~

Each peer's operation engine maintains an internal data structure that tracks
each peer's internal engine state. Thus, periodically, peers must distribute
their local engine state to remote peers. In this and other documents, the
internal state is sometimes referred to as the engine's *context vector*. The OT
API provides two methods for this: :js:func:`OTEngine.syncOutBound` and
:js:func:`OTEngine.syncInBound`.

:js:func:`OTEngine.syncOutBound` takes no arguments and returns a JSON encodable
object. This object must be sent to other peers in its exact state.

:js:func:`OTEngine.syncInBound` takes two arguments, the integer site Id of the
remote peer whose engine state we are syncing and the remote engine state
itself.

It is recommended that each peer distributes its local engine state to remote
peers every **ten** seconds.

.. _operational transform: http://en.wikipedia.org/wiki/Operational_transformation
