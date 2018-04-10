# Hardware.Reader

A reader is the logical representation of a token reader. A token may be
physical, biometric or entered through a keypad. In addition to identification a
reader may also be used to verify by using a PIN code entered through a keypad.
N.B. The term PIN is a misnomer and should really be referred to as a PVN
(Personal Verification Number).

The readers element of the controller configuration maps a logical reader
instance to a physical reader port on a blade in a controller.

````json
{
    "controller": {
        "readers": [{
                "id": "0CB35262-BA8C-4482-B146-2A7FBCD69FC6",
                "description": "Reader 1",
                "address": "1-0-1",
                "operationalMode": "tokenOnly",
                "tokenFormatType": "RDSATEONPRO",
                "digitsForPin": 4,
                "readerTamperType": "disabled",
                "enterPinPeriod": "00:20.00",
                "validLedType": "activeHigh",
                "beeperType": "activeHigh",
                "readerTamperType": "unsupervised",
                "validReadLedPeriod": "00:00:03",
                "validReadBeeperPeriod": "00:00:00.20",
                "invalidReadBeeperPeriod": "00:00:03",
                "invalidReadBeeperMode": "urgentPulse",
                "changeModePermissions": ["Operator"]
            }
        ]
    }
}

````

The type name for readers is Hardware.Reader

## Properties

### address

**[hardware-address]** The controller logical address for the reader

### operationalMode

**[enum]** A reader can be configured to be in a specific operational mode. The
reader can be commanded to change its mode at any time, a trigger based on a
time schedule would be a common example. This property sets the default mode the
reader will operate in. The available operational modes are:

- TokenOnly – Only the token number is required.
- TokenAndPin – Both a token number and PIN verification is required.
- TokenViaKeypadAndPin - Both token number and PIN verification is required.
    Optionally, the token number can be entered using the keypad.
- Disabled – All reads from the reader will be ignored.

### changeModePermissions

**[string[]]** A list of permissions. To change the Operational Mode you need a
permission on this list to grant you permission.

### tokenFormatType

**[string (10000)]** This field can either be the name of one of the predefined
reader types, such as ‘RDSATEONPRO’ or it can be XML that defines a custom
reader format.

### readerTamperType

**[enum]** How the reader tamper input should be configured. Valid values are:

- disabled
- unsupervised (default)
- supervised

### validLedType

**[emum]** How the valid LED output should be configured. Valid values are:

- activeHigh (default)
- activeLow

### beeperType

**[emum]** How the beeper output should be configured. Valid values are:

- activeHigh (default)
- activeLow

### enterPinPeriod

**[timespan]** The period the reader will wait for a PIN to be entered. Default is
20 seconds. The LED output mode will be non-urgent. For a person with the ‘Blind
or Partially Sighted’ attribute the beeper output mode is non-urgent.

### digitsForPin

**[int]** The number of digits required for the PIN. Default is 4.

### validReadLedPeriod

**[timespan]** Default is 3 seconds.

### validReadBeeperPeriod

**[timespan]** Default is 200ms. For a person with the ‘Blind or Partially Sighted’
attribute the Output Mode is always Constant.

### invalidReadBeeperPeriod

**[timespan]** Default is 3 seconds. For a person with the ‘Blind or Partially
Sighted’ attribute the Output Mode is always Urgent Pulse.

### invalidReadBeeperMode

**[enum]** The audio pattern the beeper should produce. Valid values are:

- Disabled
- Constant
- UrgentPulse (default)
- NonUrgent Pulse
- ReminderPulse

### States

### OperationalMode

**[enum]** This shows the current reader mode (see Reader Operational Modes for
details).

### ConnectedToBlade

**[boolean] [diagnostic]** Is the reader available on a connected blade.

### Tamper

**[enum]** This shows the state of the reader tamper. Valid values are:

- Inactive
- Active
- Error
- unknown

## Events

The following events are sent from the reader. All events have the reader
identity as their event source. Each event carries extra data in its payload,
which is listed with each event.

### ChangeMode

A request was made to change the operational mode of the reader.

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       | OperationalMode   |
| FailedOnPermissions  | NoPermisions          | OperationalMode   |
|                      | NoRelevantPermissions | OperationalMode   |
|                      | NoActivePermissions   | OperationalMode   |
| CommandArgumentError |                       | OperationalMode   |

### Read

A token was present to the read and successfully decoded.

The read event has a discriminator (Result) that describes the outcome of the
read. The other items included in the event payload will depend on the value of
the Result. These include:

- TokenData [string] – A textual representation of the data from the reader if
    possible, for a weigand reader this will likely be the card number, an ANPR
    system the car registration. If the reader is some form biometrics this
    value will contain a static string like ‘finger’
- TokenId [identifier] – if the value read from the reader could be matched to
    a value in the database, this is the unique identifier for that token.
- PersonId [identifier] – The identifier for the person the token was
    associated with

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**                |
|------------------|----------------------------------|
| Success          | TokenData, TokenId, PersonId     |
| PinTimeout       | TokenData, [TokenId], [PersonId] |
| DuressPinUsed    | TokenData, TokenId, PersonId     |
| IncorrectPin     | TokenData, TokenId, PersonId     |
| UnknownToken     | TokenData                        |
| UnknownPerson    | TokenData, TokenId               |
| TokenNotEnabled  | TokenData, TokenId, PersonId     |
| PersonNotEnabled | TokenData, TokenId, PersonId     |
| ReaderDisabled   | TokenData, [TokenId], [PersonId] |

## Commands

### ChangeOperationalMode

This command is used to request a change the operational mode or cancel an
existing command. All command requests are put on a list. The operational mode
is set to the command with the highest priority at any one time. If a command
times-out or is cancelled, it is taken off the list and the mode is revaluated.
With no commands on the list the mode goes to the default. To cancel a command
only the reference argument should be supplied.

Command Arguments:

- Mode – Required mode
- Period – Period that it lasts (optional)
- Priority – 0 to 255, 0 being the highest
- ReferenceId – Controlling Entity
