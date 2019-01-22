# Entity Types

The following entity types are used by the controller

- [AccessControl.Area](AccessControlArea.md)
- [AccessControl.Portal](AccessControlPortal.md)
- [AccessControl.PortalInterlock](AccessControlPortalInterlock.md)
- [AccessControl.User](AccessControlUser.md)
- [Common.Action](CommonAction.md)
- [Common.SystemMode](CommonSystemMode.md)
- [Common.TimeTable](CommonTimeTable.md)
- [Hardware.Blade](HardwareBlade.md)
- [Hardware.Controller](HardwareController.md)
- [Hardware.Input](HardwareInput.md)
- [Hardware.Output](HardwareOutput.md)
- [Hardware.Reader](HardwareReader.md)
- [Osdp.Reader](OsdpReader.md)
- [SmartIntego.Gateway](SmartIntegoGateway.md)
- [SmartIntego.Portal](SmartIntegoPortal.md)
- [SmartIntego.Reader](SmartIntegoReader.md)
- [SmartIntego.System](SmartIntegoSystem.md)

## Common Properties

All entities will have the following common properties unless otherwise noted.

### id

**[string (50)]** - An immutable, case-insensitive 50 character string that is used to link objects together within the controller.  Identifiers should be limited to the characters:

- a-z
- A-Z
- 0-9
- \-

### description

**[string (200)]** - A string used to provide a human readable description of an object, with the current version of the Access Controller, this is optional and not used, but may be used when we add OSDP support for output displays.

### type

**[string (200)]** - Most objects have a default type and this option can be omitted. An example where this has to be used is `Osdp.Reader` vs the default `Hardware.Reader`. There are other examples.

### operationalMode

**[enum]** - Sets default operational mode. Not all entities have an operational mode, and the values are dependant on the entity type.

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

Command messages are sent from the server to the controller to cause a change in state within the controller, or tell the controller to perform an action.

### Command Progress

Progress messages are linked to the originating command by setting the previous
message id to the message id of the source command.

### Command Response

Response messages are linked to the originating command by setting the previous
message id to the message id of the source command

## Operational Modes

This command is used to request a change the operational mode or cancel an
existing command. All command requests are put on a list. The operational mode is set to the command with the highest priority at any one time. If a command times-out or is cancelled, it is taken off the list and the mode is re-evaluated. With no commands on the list the mode goes to the default. To cancel a command only the reference argument should be supplied.

Command Arguments:

- Mode – Required mode
- Period – Period that it lasts (optional)
- Priority – 0 to 255, 0 being the highest
- ReferenceId – Controlling Entity
- PermissionOverrideId (optional)