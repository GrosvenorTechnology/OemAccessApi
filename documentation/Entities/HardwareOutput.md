# Hardware.Output

An output is a simple On/Off relay device (except for a door blade output, which is solid-state). It can be used by other entities, directly commanded or commanded through actions.

There are two main ways of commanding an output. The recommended way is by changing its operational mode (between 'Off' and 'On', making the default 'Off'). With a ChangeMode command you can set a period, priority and a reference, so it will work well with several command sources. The alternative method is to keep the operational mode 'Normal' and use the other commands listed below.

The output has the following operational modes:

- **normal** (Default) – The output state will change in response to commands.
- **off** - The output state will be off. The output will only respond to ChangeMode commands.
- **on** - The output state will be on. The output will only respond to ChangeMode commands.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

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

**[hardware-address]** The controller logical address for the output

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

- **Period [timespan] (infinite) (optional)** – How long should the output stay on.
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
