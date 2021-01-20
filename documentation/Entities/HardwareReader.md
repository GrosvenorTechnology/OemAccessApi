# Hardware.Reader

## Introduction

A reader is the logical representation of a token reader. A token may be
physical, bio-metric or entered through a keypad. In addition to identification a
reader may also be used to verify by using a PIN code entered through a keypad.
N.B. The term PIN is a misnomer and should really be referred to as a PVN
(Personal Verification Number).

The readers element of the controller configuration maps a logical reader
instance to a physical reader port on a blade in a controller.

### Reader Commands

Keypad readers also support the concept of 'Reader Commands'. These commands are not sent directly to an associated portal, but can be picked-up and used by Actions. An Action could then command a portal to change its mode to 'unlocked' or 'disabled', or do something completely different. To enter a reader command the user enters the command number followed by `Enter` (or `#`), followed by a token read (and then a PIN, if required).

### Modes of Operation

A reader can be configured to be in a specific operational mode. The
reader can be commanded to change its mode at any time, a trigger based on a
time schedule would be a common example. This property sets the default mode the
reader will operate in. The available operational modes are:

- **tokenOnly** – Only the token number is required.
- **tokenAndPin** – Both a token number and PIN verification is required.
- **tokenViaKeypadAndPin** - Both token number and PIN verification is required.
  Optionally, the token number can be entered using the keypad.
- **disabled** – All reads and tamper from the reader will be ignored.
- **isolated** – All reads from the reader will be ignored.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

### Example configuration

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
                "enterPinPeriod": "00:00:20",
                "validLedType": "activeHigh",
                "beeperType": "activeHigh",
                "readerTamperType": "unsupervised",
                "validReadLedPeriod": "00:00:03",
                "validReadBeeperPeriod": "00:00:00.20",
                "invalidReadBeeperPeriod": "00:00:03",
                "invalidReadBeeperMode": "urgentPulse",
                "beepOnValidRead": "impairedSight",
                "changeModePermissions": ["Operator"]
            }
        ]
    }
}

````

The type name for readers is Hardware.Reader

## Properties

### address

**[hardware-address]** The controller logical address for the reader.

### operationalMode

**[enum] (tokenOnly)** Specifies which operational mode is the default.

### tokenFormatType

**[string (100)] (RDSATEONPRO)** The name of tokenFormat to use, the default
`RDSATEONPRO` is a built in type to support `Sateon Pro` readers. Use the
`tokenFormats` section in the application config to define custom formats.

### digitsForPin

**[int] (4)** The number of digits required for the PIN.

### enterPinPeriod

**[timespan] (00:00:20)** The period the reader will wait for a PIN to be entered.
The LED output mode will be non-urgent. For a person with the `Blind`
or `Partially Sighted` attribute the beeper output mode is non-urgent.

### validLedType

**[enum] (activeHigh)** How the valid LED output should be configured.

- **activeHigh**
- **activeLow**

### beeperType

**[enum] (activeHigh)** How the beeper output should be configured.

- **activeHigh**
- **activeLow**

### readerTamperType

**[enum] (unsupervised)** How the reader tamper input should be configured.

- **disabled**
- **unsupervised**
- **supervised**

### validReadLedPeriod

**[timespan] (00:00:03)** How long the valid LED stays on for a valid read.

### validReadBeeperPeriod

**[timespan] (00:00:00.20)** For a person with the ‘Blind or Partially Sighted’
attribute the Output Mode is always Constant.

### invalidReadBeeperPeriod

**[timespan] (00:00:03)** For a person with the `Blind` or `Partially Sighted`
attribute the output mode is always `urgentPulse` and overrides whatever has been
configured in `invalidReadBeeperMode`.

### invalidReadBeeperMode

**[enum] (urgentPulse)** The audio pattern the beeper should produce.

- **disabled**
- **constant**
- **urgentPulse**
- **nonUrgentPulse**
- **reminderPulse**

### beepOnValidRead

**[enum] (impairedSight)** Determines whether/when the reader beeper beeps on a valid read.

- **never**
- **impairedSight**
- **always**

### changeModePermissions

**[string[]] (empty)** A list of permissions. To change the Operational Mode you need a
permission on this list to grant you permission.

## States

### OperationalMode

**[enum]** This shows the current reader mode (see Reader Operational Modes for
details).

### ConnectedToBlade

**[boolean] [diagnostic]** Is the reader available on a connected blade.

### ReaderTamperState

**[enum]** This shows the state of the reader tamper. Valid values are:

- inactive
- active
- error
- unknown

## Events

The following events are sent from the reader. All events have the reader
identity as their event source. Each event carries extra data in its payload,
which is listed with each event.

### Read

A token was present to the read and successfully decoded.

The read event has a discriminator (Result) that describes the outcome of the
read. The other items included in the event payload will depend on the value of
the Result. These include:

- **TokenData [string]** – A textual representation of the data from the reader if
    possible, for a Wiegand reader this will likely be the card number, an ANPR
    system the car registration. If the reader is some form biometrics this
    value will contain a static string like ‘finger’.
- **TokenId [identifier]** – if the value read from the reader could be matched to
    a value in the database, this is the unique identifier for that token.
- **PersonId [identifier]** – The identifier for the person the token was
    associated with.
- **Data [string]** - A tokens raw binary data received from reader.

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
| ReaderIsolated   | TokenData, [TokenId], [PersonId] |
| RejectedFormat   | Data                             |

>N.B. Reader Commands have the event name of `Read:n` where 'n' is the command number 1 to 9. See above for information on Reader Commands.

## Commands

None.
