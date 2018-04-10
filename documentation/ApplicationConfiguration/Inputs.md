# Inputs

The input types track the physical input and present a logical view of the input
state. All inputs are ‘debounced’, and will not show a change of state until the
physical input has been stable in the new state for at least 75ms.

The input has the following operational modes:

- **Enabled** (Default) – The input state will change in response to changes to the
    physical input.
- **OnlyMonitorCircuit** - The physical input will only be monitored for error
    states.
- **Disabled** - All changes to the physical input will be ignored.

````json 
{
    "controller": {
        "inputs": [{
            "id": "58B36B10-1407-4216-B45F-185E523DD09D",
            "description": "Input 1",
            "operationalMode": "normal",
            "address": "1-0-1",
            "normallyOpen": true,
            "supervised": true,
            "sensePeriod": "00.0100",
            "pirInhibit": "5:00.00",
            "pirFault": "5:00.00"
            }
        ]
    }
}
````

## Properties

### address 

**[hardware-address]** The controller logical address for the input

### operationalMode

[enum] An input can be configured to be in a specific operational mode. The
input can be commanded to change its mode at any time, a trigger based on a time
schedule would be a common example. This property sets the default mode the
input will operate in. The available operational modes are:

-   Normal – The logical state of the input reflects the physical state.

-   PIR – Smooth’s out the chatter from a PIR.

-   Disabled – The input will stay in the current state regardless pf the state
    of the physical input.

### sensePeriod

**[timespan]** The time the input must be stable in a new state before the logical
state of the input is changed.

### supervised 

**[Boolean]** If false, the input is a basic Open/Closed type input, if true that
input is a 4 state input with open/closed/fault/tamper support.

### normallyOpen

**[Boolean]** If true the input will show a logical state of *Reset* when the input
is Open.

### pirInhibit

**[timespan]** The amount of time the input’s physical input, when in PIR mode, must
be reset before the input state will change to reset.

### pirFault

**[timespan]** The maximum amount of time the input’s physical input, when in PIR
mode, can stay active before the input is placed in the *Fault* state.

## States

### OperationalMode

**[enum]** This shows the current reader mode (see Input Operational Modes for
details).

### ConnectedToBlade

**[boolean]** [diagnostic] Is the reader available on a connected blade.

### InputState 

**[enum]** The logical state of the input, valid values are

-   Inactive
-   Active
-   Error
-   Unknown

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
