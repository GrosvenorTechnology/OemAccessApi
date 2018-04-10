# Hardware.Input

The input types track the physical input and present a logical view of the input
state. All inputs are ‘debounced’, and will not show a change of state until the
physical input has been stable in the new state for at least 75ms.

The input has the following operational modes:

- **enabled** (Default) – The input state will change in response to changes to the
    physical input.
- **onlyMonitorCircuit** - The physical input will only be monitored for error
    states.
- **disabled** - All changes to the physical input will be ignored.

````json 
{
    "controller": {
        "inputs": [{
            "id": "Kitchen-PIR",
            "description": "Input 1",
            "operationalMode": "enabled",
            "address": "1-0-1",
            "normallyOpen": true,
            "inputType": "unsupervised",
            "InputOperationType": "normal",
            "sensePeriod": "00:00:00.1",
            "pirInhibit": "00:30:00",
            "pirFault": "5:00:00",
            "changeModePermissions": ["Operator"]
        }]
    }
}
````

## Properties

### address 

**[hardware-address]** The controller logical address for the input

### operationalMode

**[enum]** Specifies which operational mode is the default.

### sensePeriod

**[timespan]** The time the input must be stable in a new state before the logical
state of the input is changed.

### inputType 

**[enum] (unsupervised)** Configures the input type.

- Unsupervised - The input is a basic Open/Closed type input.
- Supervised - The input is a 4 state input with open/closed/fault/tamper support.

### inputOperationType 

**[enum] (normal)** Configures how the input handles changes in the input state.

- Normal - When the physical input changes state, the logical state is updated
- OutputPulseWhenTriggered - The same as Normal but also pulses the corresponding output (.e. input 1-0-1, triggers output 1-0-1) 
- PirDetector - Enables special handling for PIR type inputs.

### normallyOpen

**[Boolean]** If true the input will show a logical state of *Reset* when the input
is Open.

### pirInhibit

**[timespan]** The amount of time the input’s physical input, when in PIR mode, must
be reset before the input state will change to reset. Only used when `InputOperationType` is set to `PirDetector`

### pirFault

**[timespan]** The maximum amount of time the input’s physical input, when in PIR
mode, can stay active before the input is placed in the *Fault* state. Only used when `InputOperationType` is set to `PirDetector`

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
