# Hardware.Input

The input types track the physical input and present a logical view of the input
state. All inputs are ‘de-bounced’, and will not show a change of state until the
physical input has been stable in the new state for at least 75ms.

The input has the following operational modes:

- **enabled** (Default) – The input state will change in response to changes to the
    physical input.
- **onlyMonitorCircuit** - The physical input will only be monitored for error
    states.
- **disabled** - All changes to the physical input will be ignored.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

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
            "pirInhibit": "00:30:00",
            "pirFault": "5:00:00",
            "changeModePermissions": ["Operator"]
        }]
    }
}
````

## Properties

### operationalMode

**[enum] (enabled)** Specifies which operational mode is the default.

### address

**[hardware-address] [required]** The controller logical address for the input

### normallyOpen

**[Boolean] (true)** If true the input will show a logical state of *Reset* when the input
is Open.

### inputType

**[enum] (unsupervised)** Configures the input type.

- **unsupervised** - The input is a basic Open/Closed type input.
- **supervised** - The input is a 4 state input with open/closed/fault/tamper support.

### inputOperationType

**[enum] (normal)** Configures how the input handles changes in the input state.

- **normal** - When the physical input changes state, the logical state is updated
- **outputPulseWhenTriggered** - The same as Normal but also pulses the corresponding
  output (.e. input 1-0-1, triggers output 1-0-1)
- **pirDetector** - Enables special handling for PIR type inputs.

### pirInhibit

**[timespan] (00:30:00)** The amount of time the input's physical input, when in PIR
mode, must be reset before the input state will change to reset. Only used when
`InputOperationType` is set to `PirDetector`

### pirFault

**[timespan] (5:00:00)** The maximum amount of time the input's physical input, when
in PIR mode, can stay active before the input is placed in the *Fault* state. Only used
when `InputOperationType` is set to `PirDetector`

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the
`ChangeMode` command.

## States

### OperationalMode

**[enum]** This shows the current mode (see Input Operational Modes for
details).

### ConnectedToBlade

**[boolean] [diagnostic]** Is the input available on a connected blade.

### InputState

**[enum]** The logical state of the input, valid values are

- **Inactive**
- **Active**
- **Error** - If the input is in a supervised mode, this state means the input
  is in a tamper, open circuit or short circuit state.
- **Unknown**

## Events

None.

## Commands

None.