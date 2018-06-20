# Hardware.Blade

Supported in V1.1

TODO: Page needs work!!

The Portal Interlock has the following operational modes:

- **enabled** (Default)
- **lockDown**
- **disabled**

## Properties

None.

## States

### OperationalMode

**[enum]** This shows the current operational mode.

### InterlockState

**[enum]** State of the Interlock.

- **idle**
- **interlock**
- **breached**

## Events

No standalone events.

## Commands

### ChangeMode

Add or remove an entry from the operational mode stack of the interlock.

Add Entry to stack

- **Mode [entityId]** - The mode to change to.
- **Priority [int]** - The priority for the mode entry.
- **Period [timespan] (optional)** - If provided the entry will be automatically
  removed after the given time period.
- **Reference [string] (optional)** - A reference that can be used to remove the
  entry from the stack.

Remove entry from stack

- **Reference [string] (optional)** - Remove the entry with the matching reference
  from the stack.

Depending on the result of the command the following items will be present in the
event contents.

| **Result**           | **Reason**            |   **Event Content** |
|----------------------|-----------------------|---------------------|
| Success              |                       | [Mode]              |
| FailedOnPermissions  | NoPermissions          | [Mode]              |
|                      | NoRelevantPermissions | [Mode]              |
|                      | NoActivePermissions   | [Mode]              |
| CommandArgumentError |                       | [Mode]              |
