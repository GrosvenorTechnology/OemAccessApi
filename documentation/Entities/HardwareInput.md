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
            "pirInhibit": "00:00:30",
            "pirFault": "00:05:00",
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

### sensePeriod

**Supported >= 4.2.0**.

**[timespan] (00:00:00.05)** Defines the number of period before the input is deemed to be in the active state. The value can be from 50ms to 1 hour. Often used to ensure a non controlled fire door is not wedged open. A sense period of 30 seconds ensures the monitoring input does not go into alarm unless the door is open for more than this time.

### inputType

**Supported >= 4.2.0**.

**[enum] (unsupervised)** Configures the input type.

- **unsupervised** - The input is a basic Open/Closed type input.
- **supervised** - The input is a 4 state input with open/closed/error support.
- **custom** - The input is configured by defining the `customInputBandDefinitions` property. When using this input type the `normallyOpen` and `sensePeriod` properties are not required and are ignored.

### customInputBandDefinitions

Only used when the `inputType` is defined as `custom`. It allows the transition band voltages of an input to be defined along with the sense periods.

There are four bands: `shortCircuit`, `inactive`, `active` and `openCircuit`. The `shortCircuit` and `openCircuit` can be omitted if the input should work like an unsupervised input. The `shortCircuit` minimum is fixed to 0V and the `openCircuit` maximum is fixed to the maximum voltage 5.5V.

The `minVoltage` and `maxVoltage` values are in millivolts and configure the exit voltage levels. The `sensePeriod` value defines the time before switching into a new band. The time must be between 50ms and 1 hour.

An input switches to a different band, if the voltage level is outside the
min/max values of the current band for more than the `sensePeriod` time of the new band.

The two or four band definitions need to cover the complete voltage range
from 0V to 5.5V and leave no gaps. An overlap is allowed and allows for a hysteresis to be defined.

See examples below: (The values shown are the default values the `unsupervised` and `supervised` input types use internally)

````json
{
        "customInputBandDefinitions": {
            "inactive": {"minVoltage": 0, "maxVoltage": 2550, "sensePeriod": "00:00:00.05"},
            "active": {"minVoltage": 2450, "maxVoltage": 5500, "sensePeriod": "00:00:00.05"}
        }
}
````

````json
{
        "customInputBandDefinitions": {
            "shortCircuit": {"maxVoltage": 1235, "sensePeriod": "00:00:00.05"},
            "active": {"minVoltage": 1188, "maxVoltage": 1978, "sensePeriod": "00:00:00.05"},
            "inactive": {"minVoltage": 1910, "maxVoltage": 2500, "sensePeriod": "00:00:00.05"},
            "openCircuit": {"minVoltage": 2460,  "sensePeriod": "00:00:00.05"}
        }
}
````

### inputOperationType

**[enum] (normal)** Configures how the input handles changes in the input state.

- **normal** - When the physical input changes state, the logical state is updated
- **outputPulseWhenTriggered** - The same as Normal but also pulses the corresponding
  output (.e. input 1-0-1, triggers output 1-0-1)
- **pirDetector** - Enables special handling for PIR type inputs.

### pirInhibit

**[timespan] (00:00:30)** The amount of time the input's physical input, when in PIR
mode, must be reset before the input state will change to reset. Only used when
`InputOperationType` is set to `PirDetector`

### pirFault

**[timespan] (00:05:00)** The maximum amount of time the input's physical input, when
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
