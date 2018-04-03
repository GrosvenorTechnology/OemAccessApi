![](media/0abe22ee2f204ba44e787a8acd1d1bd9.png)

# Domain Model

## Overview / Basics

Throughout this document the term ‘User’ will be used to mean an entity with
access permissions associated to them. In practice this may be a
User/Visitor/Car/Asset, but User is the most natural for descriptive purposes.

## Common Properties

All objects will have the following common properties unless otherwise noted.

### id

[string (50)] : An immutable, case-insensitive 50 character string that is used
to link objects together within the controller. Identifiers should be limited to
the characters:

- a-z
- 0-9
- \-

### Description

[string (200)] : A string used to provide a human readable description of an
object, with the current version of the Access Controller, this is optional and
not used, but may be used when we add OSDP support for output displays.

### Type

[string (200)] : Most objects have a default type and this option can be
omitted, an example where this might be used is AccessControl.User vs
AccessControl.Visitor which largely can be used interchangeably, but extra
behaviour is added for Visitor objects.

## States

States messages represent the current value of a volatile part of the system,
for example if an input is Active or Reset, if a door is forced or wedged or an
area is at or over maximum occupancy. All these values are expressed and
transported through the same type of message in the system.

All states are raised from a source entity, and details of the individual states
are described in the section that describes the source entity (such as reader).

### StateNotification

A state notification is sent from the controller to the service when a value is
changed.

### StateRequest

A request can be sent to the controller to query the current value of any state.

## Commanding

Commands are used to cause a change in state of the system, for example to
operate an output or unlock a door. Commands are sent to the controller, and the
controller responds with zero or more CommandProgress messages and finally a
CommandResponse message that signals success or failure of the command.

Command-\> [0-\*] CommandProgress -\> CommandResponse

All commands are directed at a specific entity, and details of the individual
commands are described in the section that describes the target entity (such as
portal).

### Command

### Command Progress

Progress messages are linked to the originating command by setting the previous
message id to the message id of the source command.

### Command Response

Response messages are linked to the originating command by setting the previous
message id to the message id of the source command

## Application Configuration
