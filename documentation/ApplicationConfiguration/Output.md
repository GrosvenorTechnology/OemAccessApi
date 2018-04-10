# Output

An output is a simple On/Off relay device.

The output has the following operational modes:

- **Normal** – The output state will change in response to commands.

````json
{
    "controller": {
        "outputs": [{
            "id": "Reception-Lights",
            "description": "Reception Lights",
            "operationalMode": "reset",
            "address": "1-0-1",
            "pulseLength": "00:10"
            }
        ]
    }
}
````

## Properties

### address 

**[hardware-address]** The controller logical address for the input

### operationalMode

**[enum]** An output can be configured to be in a specific operational mode. The
output can be commanded to change its mode at any time, a trigger based on a
time schedule would be a common example. This property sets the default mode the
output will operate in. The available operational modes are:

- Reset – The output is ‘off’
- Operated – The output is ‘on’
- Disabled – The output will stay in it’s current state regardless of any
commands sent to it.

### pulseLength

**[timespan]** The default pulse length used when a pulse command is actioned.

## States

### OperationalMode

**[enum]** This shows the current operational mode for the output.

### ConnectedToBlade

**[boolean]** **[diagnostic]** Is the reader available on a connected blade.

### OutputState

**[enum]** The logical state of the input, valid values are

-   On
-   Off

## Events

### ChangeMode

A request was made to change the operational mode of the input.

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       | OperationalMode   |
| FailedOnPermissions  | NoPermisions          | OperationalMode   |
|                      | NoRelevantPermissions | OperationalMode   |
|                      | NoActivePermissions   | OperationalMode   |
| CommandArgumentError |                       | OperationalMode   |

## Commands

### ChangeOperationalMode

This command is used to request a change the operational mode or cancel an
existing command. All command requests are put on a list. The operational mode
is set to the command with the highest priority at any one time. If a command
times-out or is cancelled, it is taken off the list and the mode is revaluated.
With no commands on the list the mode goes to the default. To cancel a command
only the reference argument should be supplied.

Command Arguments:

-   Mode – Required mode
-   Period – Period that it lasts (optional)
-   Priority – 0 to 255, 0 being the highest
-   ReferenceId – Controlling Entity