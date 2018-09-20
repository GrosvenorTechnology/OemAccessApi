# Operational Mode Overview

Most entity types have an operational mode. The operational mode differs from a normal state in that, behind the scenes, there is a stack containing requested operational mode states. This stack is prioritised, where the entry with the highest priority (closest to zero) wins and becomes the current operational mode. If there is nothing on the stack the default operational mode is used. The default can be changed in the entity's configuration.

## Example

To help make this a bit clearer, lets look at an example for a portal.

The default operational mode is always used when there is nothing on the stack, in this case it is `Normal`.

We want the door to be unlocked between 09:00-17:00, monday to friday. One way of doing this is to create a `TimeTable` and call it something like "M-F-9-17". Now using the Portal "UnlockOnTimeTable" property, we can set the Time Table to "M-F-9-17" and the priority to 100 (an arbitrary number). The controller will manage adding and removing the unlock entries for us, so at lunch time in Wednesday the stack will look as follows:

| Mode     | Priority | Reference    |
|----------|----------|--------------|
| Unlocked | 100      | M-F-9-17     |

Here the highest priority (and only) entry, `Unlocked`, will win and the portal will be unlocked, when 17:00 comes around in the evening, the controller will remove that entry and the door will return to the default `Normal` (locked) state.

Now should something happen, and the building must be locked down. A guard (or automated action) can disable (lock the portal and disable any access) by placing the portal into the `Disabled` mode and using a higher priority:

| Mode     | Priority | Reference    |
|----------|----------|--------------|
| Disabled | 30       | UserCommand  |
| Unlocked | 100      | M-F-9-17     |

The portal will immediately lock and remain disabled, even when the schedule attempts to return the door to normal state, as it's not actually commanding the door to `Normal` mode, but is just removing it's `Unlocked` entry:

| Mode     | Priority | Reference    |
|----------|----------|--------------|
| Disabled | 30       | UserCommand  |

The highest priority (and only) entry remains the disable command until the guard 'resets' the door by removing his entry. The door then goes to its default operational state `Normal`.

## Commands affecting Operational Mode

### ChangeMode

Add or remove an entry from the operational mode stack.

Add Entry to stack:

- **Mode [entityId]** - The mode to change to.
- **Priority [int]** - The priority for the mode entry.
- **Period [timespan] (optional)** - If provided the entry will be automatically removed after the given time period.
- **Reference [string] (optional)** - A reference that can be used to remove the entry from the stack. If no reference is supplied it will normally default to the source entity `type:id`. An Action will supply a reference dependant on its configuration.

Remove entry from stack:

- **Reference [string] (optional)** - Remove the entry with the matching reference from the stack. If no reference is supplied it will normally default to the source entity `type:id`. An Action will supply a reference dependant on its configuration.

The result of a command can be:

| **Result**           | **Reason**            |   **Event Content** |
|----------------------|-----------------------|---------------------|
| Success              |                       | [Mode]              |
| FailedOnPermissions  | NoPermissions         | [Mode]              |
|                      | NoRelevantPermissions | [Mode]              |
|                      | NoActivePermissions   | [Mode]              |
| CommandArgumentError |                       | [Mode]              |

### ClearModeStack

Clears the operational mode stack. The operational mode will be set to the default. This command should only be used by support engineers.

The result of a command can be:

| **Result**           | **Reason**            |
|----------------------|-----------------------|
| Success              |                       |
| FailedOnPermissions  | NoPermissions         |
|                      | NoRelevantPermissions |
|                      | NoActivePermissions   |

### ClearOwnedFromModeStack

Clears the operational mode stack of entries initiated by the source entity. The operational mode will be set using the normal rules.

This command can be used as a "restoreCommand" within actions.

The result of a command can be:

| **Result**           | **Reason**            |
|----------------------|-----------------------|
| Success              |                       |
| FailedOnPermissions  | NoPermissions         |
|                      | NoRelevantPermissions |
|                      | NoActivePermissions   |
