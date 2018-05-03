# Application Configuration

The Application Configuration is a JSON document that defines the behaviour of the access controll logic portion of the application.

## Overview

- **User** - Throughout this document the term ‘User’ will be used to mean an entity with access permissions associated to them. In practice this may be a User/Visitor/Car/Asset, but User is the most natural for descriptive purposes. 
- [Data Types](../Entities/DataTypes.md) - All the data types used are defined here.

## File Structure 

The Application Configuration is comprised of a single root controller node with a slection of sub nodes that can only appear once, such as readers and portals.  The controller type can have it's own configuration properties as well.

The various sections can be defined in the controller node in any order. 

````json
{
    "controller": {
        "readers": [...],
        "portals": [...],
        "inputs": [...],
        "outputs": [...],
        ...
    }
}
````

## File Sections

- [controller](../Entities/HardwareController.md)
- [readers](../Entities/HardwareReader.md)
- [portals](../Entities/AccessControlPortal.md)
- [inputs](../Entities/HardwareInput.md)
- [outputs](../Entities/HardwareOutput.md)
- [areas](../Entities/AccessControlArea.md)
- [tokenFormats](TokenFormat.md)
- [timeTables](../Entities/CommonTimeTable.md) [>=V1.0.19635]

## Common Properties

All objects will have the following common properties unless otherwise noted.

### id
**[string (50)]** - An immutable, case-insensitive 50 character string that is used to link objects together within the controller.  Identifiers should be limited to the characters:
- a-z
- 0-9
- \-

### description
**[string (200)]** - A string used to provide a human readable description of an object, with the current version of the Access Controller, this is optional and not used, but may be used when we add OSDP support for output displays.

### type
**[string (200)]** - Most objects have a default type and this option can be omitted, an example where this might be used is AccessControl.User vs AccessControl.Visitor which largely can be used interchangeably, but extra behaviour is added for Visitor objects.

## States

States represent the current value of a volatile part of the system,
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

Command messages are sent from the server to the controller to casue a change in state 
within the controller, or tell the controller to perform an action.

### Command Progress

Progress messages are linked to the originating command by setting the previous
message id to the message id of the source command.

### Command Response

Response messages are linked to the originating command by setting the previous
message id to the message id of the source command
