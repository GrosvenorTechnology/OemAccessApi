# Hardware.Blade

TODO: Page needs work!!

The blade has the following operational modes:

- **normal** (Default)

## Properties

None.

## States

### OperationalMode

**[enum]** This shows the current mode (see Input Operational Modes for
details).

### ConnectedToBlade

**[boolean] [diagnostic]** Is the input available on a connected blade.

### BladePartNumber

**[string]** The part code of the blade.

### BladeHardwareRevision

**[string]** The blade hardware revision.

### BladeFirmwareType

**[string]** The blade firmware type.

### BladeFirmwareVersion

**[string]** The blade firmware version.

### V12CurrentLimitState

**[enum]** Has the 12V current limit been tripped.

- **tripped**
- **cleared**

### V12OutCurrentLimitState

**[enum]** Has the 12V current limit been tripped.  TODO: What's the difference between this and V12CurrentLimitState?

- **tripped**
- **cleared**

### V12VInVoltage

**[enum]** State of the 12V rail voltage to the blade

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V12InCurrent

**[enum]** State of the 12V rail current being used by the blade

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### LocksVoltage

**[enum]** State of the Lock power input for the blade

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5RdrSupply

**[enum]** TODO: description needed!

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5ExtSupply

**[enum]** TODO: description needed!

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5Ext1Supply

**[enum]** TODO: description needed!

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5Ext2Supply

**[enum]** TODO: description needed!

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5UsbSupply

**[enum]** TODO: description needed!

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

## Events

### FirmwareVersion

At blade start-up, reports on the state of the blade firmware.

- **FirmwareVersion [string]** – The current firmware version.
- **NewFirmwareVersion [string]** – The current firmware version.
- **OldFirmwareVersion [string]** – The version the firmware will be updated to.

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**                      |
|------------------|----------------------------------------|
| Correct          | FirmwareVersion                        |
| Wrong            | NewFirmwareVersion, OldFirmwareVersion |

### FirmwareUpload

The blade firmware update progress.

- **FirmwareVersion [string]** – The current firmware version being updated to.

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**                      |
|------------------|----------------------------------------|
| Started          | FirmwareVersion                        |
| Success          | FirmwareVersion                        |
| Failed           | FirmwareVersion                        |

### BladeFirmware

The blade is not a known type.

- **FirmwareType [string]** – The firmware type detected in the blade.

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**     |
|------------------|-----------------------|
| UnknownType      | FirmwareType          |

## Commands

### Reset

Reset the blade.

Depending on the result of the command the following items will be present in the
event contents.

| **Result**           | **Reason**            |   **Event Content** |
|----------------------|-----------------------|---------------------|
| Success              |                       |                     |
| FailedOnPermissions  | NoPermissions         |                     |
|                      | NoRelevantPermissions |                     |
|                      | NoActivePermissions   |                     |

### ChangeMode

Add or remove an entry from the operational mode stack of the blade.

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
| FailedOnPermissions  | NoPermissions         | [Mode]              |
|                      | NoRelevantPermissions | [Mode]              |
|                      | NoActivePermissions   | [Mode]              |
| CommandArgumentError |                       | [Mode]              |
