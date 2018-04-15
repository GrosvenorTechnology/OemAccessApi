# Hardware.Output

An output is a simple On/Off relay device.

The output has the following operational modes:

- **normal** – The output state will change in response to commands.

````json
{
    "controller": {
        "outputs": [{
            "id": "Reception-Lights",
            "description": "Reception Lights",
            "address": "1-0-1",
            "operationalMode": "normal",
            "defaultOutputMode": "constant",
            "outputStateType": "activeHigh",
            "pulseLength": "00:00:01",
            "commandPermissions": ["Operator"],
            "changeModePermissions": ["Operator"]
        }]
    }
}
````

## Properties

### address

**[hardware-address]** The controller logical address for the input

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### defaultOutputMode

**[enum] (constant)** Specifies how the output behaves when commanded to activate.

- **constant**
- **urgentPulse**
- **nonUrgentPulse**
- **reminderPulse**

### outputStateType

**[enum] (activeHigh)** TODO: What does this actually do??

- activeLow
- activeHigh

### pulseLength

**[timespan] (00:00:01)** The default pulse length used when a pulse command is actioned.

### commandPermissions

**[string[]] (empty)** A person with a permission on this list can execute any command on
this output other than `ChangeMode` command which has it's own permissions.

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the
`ChangeMode` command.

## States

### OperationalMode

**[enum]** This shows the current operational mode for the output.

### ConnectedToBlade

**[boolean]** **[diagnostic]** Is the output available on a connected blade.

### OutputState

**[enum]** The logical state of the output, valid values are

- **on**
- **off**

## Events

No Standalone events.

## Commands

### OperateForPeriod

This command will turn an output on for a given period.

- **Period [timespan] (optional)** – The default is the value of the `pulseLength`
 property from the outputs configuration.
- **OutputMode [enum] (optional)** - The default is the value of the `defaultOutputMode`
 property from the outputs configuration; the enum values also match this property.


| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       |                   |
| FailedOnPermissions  | NoPermissions         |                   |
|                      | NoRelevantPermissions |                   |
|                      | NoActivePermissions   |                   |
|                      | NoRelevantPermissions |                   |
| FailedBecauseOfError |                       |                   |
| CommandArgumentError |                       |                   |

### Operate

This command will turn an output on.

- **Period [timespan] (infinte) (optional)** – How long should the output stay on.
- **OutputMode [enum] (optional)** - The default is the value of the `defaultOutputMode`
 property from the outputs configuration; the enum values also match this property.


| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       |                   |
| FailedOnPermissions  | NoPermissions         |                   |
|                      | NoRelevantPermissions |                   |
|                      | NoActivePermissions   |                   |
|                      | NoRelevantPermissions |                   |
| FailedBecauseOfError |                       |                   |  TODO: What's the difference between this
| CommandArgumentError |                       |                   |  TODO: and this?

### Reset

This command will turn an output off.

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       |                   |
| FailedOnPermissions  | NoPermissions         |                   |
|                      | NoRelevantPermissions |                   |
|                      | NoActivePermissions   |                   |
|                      | NoRelevantPermissions |                   |
| FailedBecauseOfError |                       |                   |

### Toggle

This command will toggle the output state, e.g. on->off and vice versa.

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       |                   |
| FailedOnPermissions  | NoPermissions         |                   |
|                      | NoRelevantPermissions |                   |
|                      | NoActivePermissions   |                   |
|                      | NoRelevantPermissions |                   |
| FailedBecauseOfError |                       |                   |

### ChangeMode

Add or remove an entry from the operational mode stack of the input.

Add Entry to stack

- **Mode [entityId]** - The mode to change to.
- **Priority [int]** - The priorty for the mode entry.
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
| FailedOnPermissions  | NoPermisions          | [Mode]              |
|                      | NoRelevantPermissions | [Mode]              |
|                      | NoActivePermissions   | [Mode]              |
| CommandArgumentError |                       | [Mode]              |