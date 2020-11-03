# Application Configuration

The Application Configuration is a JSON document that defines the behaviour of the access control logic portion of the application.

## Overview

- There are application configuration files for the access control controller and the gateway (485 line header) controller.
- **User** - Throughout this document the term ‘User’ will be used to mean an entity with access permissions associated to them. In practice this may be a User/Visitor/Car/Asset, but User is the most natural for descriptive purposes.
- [Data Types](../Entities/DataTypes.md) - All the data types used are defined here.

## File Structure

The Application Configuration is comprised of a single root controller node with a selection of sub nodes that can only appear once, such as readers and portals. The controller type can have it's own configuration properties as well.

The various sections can be defined in the controller node in any order.

Format for the access control controller:

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

Format for the gateway controller:

````json
{
    "controller": {
        "polledClients": [...]
    }
}
````

## File Sections for the access control controller

- [actions](../Entities/CommonAction.md)
- [areas](../Entities/AccessControlArea.md)
- [blade](../Entities/HardwareBlade.md)
- [controller](../Entities/HardwareController.md)
- [holidays](../Entities/CommonHoliday.md)
- [inputs](../Entities/HardwareInput.md)
- [lifts](../Entities/HardwareLift.md)
- [outputs](../Entities/HardwareOutput.md)
- [readers](../Entities/HardwareReader.md)
- [portals](../Entities/AccessControlPortal.md)
- [portal interlocks](../Entities/AccessControlPortalInterlock.md)
- [system modes](../Entities/CommonSystemMode.md)
- [time tables](../Entities/CommonTimeTable.md)
- [token formats](TokenFormat.md)

## File Sections for the gateway controller

**Preview >= 4.1.0**.

- [polled clients](../Entities/HardwarePolledClient.md)

## Common Properties

All objects will have the following common properties unless otherwise noted.

### id

**[string (50)]** - An immutable, case-insensitive 50 character string that is used to link objects together within the controller.  Identifiers should be limited to the characters:

- a-z
- 0-9
- \-
- _

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

Command messages are sent from the server to the controller to cause a change in state
within the controller, or tell the controller to perform an action.

### Command Progress

Progress messages are linked to the originating command by setting the previous
message id to the message id of the source command.

### Command Response

Response messages are linked to the originating command by setting the previous
message id to the message id of the source command.

Within the command response there is a Command Outcome.

#### Command Outcome

The Command Outcome has two parts, Result and Reason. The Result can be one of the following:

- Success
- TtlExceeded
- FailedOnPermissions
- FailedBecauseOfError
- UnknownCommand
- TargetNotFound
- CommandArgumentError

There maybe no Reason part for Success messages, but this is not always the case. The Reason maybe multi-part and divided with a semi-colon ';'. There are some standard Reasons which are associated with commands using Server Permissions, they are as follows:

- ServerDecision
- ServerError
- FallBack

These reasons will be multi-part and have further explanation parts.

## Operational Mode

Most entity types have an operational mode. The operational mode differs from a normal state in that, behind the scenes, there is a stack containing requested operational mode states. This stack is prioritised, where the entry with the highest priority (closest to zero) wins and becomes the current operational mode. If there is nothing on the stack the default operational mode is used. The default can be changed in the entity's configuration. Please see the [Operational Mode Overview](ModeOverview.md) document for more information.
