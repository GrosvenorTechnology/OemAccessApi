# Osdp.Reader

**Beta >= 3.0.0** **Supported >= 3.1.0**

## Introduction

A reader is the logical representation of a OSDP token reader. A token may be
physical, bio-metric or entered through a keypad. In addition to identification a
reader may also be used to verify by using a PIN code entered through a keypad.
N.B. The term PIN is a misnomer and should really be referred to as a PVN
(Personal Verification Number).

The readers element of the controller configuration maps a logical reader
instance to a physical reader on an OSDP bus which connects to a blade in a controller.

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
- **disabled** – All communications with reader will be stopped.
- **isolated** – All reads from the reader will be ignored.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

````json
{
    "controller": {
        "readers": [{
                "id": "0CB35262-BA8C-4482-B146-2A7FBCD69FC6",
                "description": "OSDP Reader 1",
                "type": "Osdp.Reader",
                "address": "1-1-1",
                "operationalMode": "tokenOnly",
                "tokenFormatType": "RDSATEONPRO",
                "digitsForPin": 4,
                "enterPinPeriod": "00:00:20",
                "validReadLedPeriod": "00:00:03",
                "validReadBeeperPeriod": "00:00:00.20",
                "invalidReadBeeperPeriod": "00:00:03",
                "invalidReadBeeperMode": "urgentPulse",
                "beepOnValidRead": "impairedSight",
                "changeModePermissions": ["Operator"],
                "idleLedMode": "nonUrgentPulse",
                "idleLedOnColour": "amber",
                "idleLedOffColour": "off",
                "osdpDeviceKey": "303132333435363738393A3B3C3D3E3F"
            }
        ]
    }
}

````

The type name for readers is Osdp.Reader

## Properties

### address

**[hardware-address]** The controller logical address for the reader. The address format normally consists of three numbers separated by hyphens. The first number is the blade, the second is the OSDP bus (1 or 2 corresponding to which RS 485 bus on the blade) and the third is the address on the OSDP bus. The allowed address range is between 0 and 7, but only four devices are supported on one bus.

### operationalMode

**[enum] (tokenOnly)** Specifies which operational mode is the default.

### tokenFormatType

**[string (100)] (RDSATEONPRO)** The name of tokenFormat to use, the default
`RDSATEONPRO` is a built in type to support `Sateon Pro` readers. Use the
`tokenFormats` section in the application config to define custom formats.

N.B. Some OSDP readers may not require this setting as they transmit the token read data as a number (or alpha-numeric string), rather than binary data.

### digitsForPin

**[int] (4)** The number of digits required for the PIN.

### enterPinPeriod

**[timespan] (00:00:20)** The period the reader will wait for a PIN to be entered.
The LED output mode will be non-urgent. For a person with the `ImpairedSight`
attribute the beeper output mode is non-urgent.

### validReadLedPeriod

**[timespan] (00:00:03)** How long the valid LED stays on for a valid read.

### validReadBeeperPeriod

**[timespan] (00:00:00.20)** For a person with the ‘Blind or Partially Sighted’
attribute the Output Mode is always Constant.

### invalidReadBeeperPeriod

**[timespan] (00:00:03)** For a person with the `ImpairedSight`
attribute the output mode is always `urgentPulse` and overrides whatever has been
configured in `invalidReadBeeperMode`.

### invalidReadBeeperMode

**[enum] (urgentPulse)** The audio pattern the beeper should produce.

- **disabled**
- **constant**
- **urgentPulse**
- **nonUrgentPulse**
- **reminderPulse**
- **flash**

### beepOnValidRead

**[enum] (impairedSight)** Determines whether/when the reader beeper beeps on a valid read.

- **never**
- **impairedSight**
- **always**

### idleLedMode

**[enum] (nonUrgentPulse)** The pattern the LED displays when idle.

- **disabled**
- **constant**
- **urgentPulse**
- **nonUrgentPulse**
- **reminderPulse**
- **flash**

### idleLedOnColour

**[enum] (amber)** The colour the LED should be when on.

- **off**
- **red**
- **green**
- **amber**
- **blue**

### idleLedOffColour

**[enum] (amber)** The colour the LED should be when off.

- **off**
- **red**
- **green**
- **amber**
- **blue**

### osdpDeviceKey

**[stringHex16] (null)** Key to use to encrypt communications to reader. Not the preferred option. Example "303132333435363738393A3B3C3D3E3F". **N.B. Don't use this example key!**

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

N.B. Some reader manufactures use the 'Local Tamper' rather than this state to signal reader tamper.

### LocalTamper

**[enum]** This shows the state of the reader tamper. Valid values are:

- normal
- tamper
- unknown

### LocalPower

**[enum]** This shows the state of the reader power. Valid values are:

- normal
- powerFailure
- unknown

### DeviceState

**[enum]** This shows the communication state of the reader. Valid values are:

- offline
- online
- error
- unknown

### Vendor

**[string] [diagnostic]** This shows the reader's vendor ID.

### ModelNumber

**[string] [diagnostic]** This shows the reader's model number.

### Version

**[string] [diagnostic]** This shows the reader's version number.

### SerialNumber

**[string] [diagnostic]** This shows the reader's serial number.

### Firmware

**[string] [diagnostic]** This shows the reader's firmware version.

### PollCount

**[int] [diagnostic]** If enabled for blade, shows the poll count per minute for the device.

### RetryCount

**[int] [diagnostic]** If enabled for blade, shows the poll retry count per minute for the device.

### SkippedByteCount

**[int] [diagnostic]** If enabled for blade, shows the skipped byte count per minute for the device.

### SpuriousByteCount

**[int] [diagnostic]** If enabled for blade, shows the spurious byte count per minute for the device.

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
    value will contain a static string like ‘finger’
- **TokenId [identifier]** – if the value read from the reader could be matched to
    a value in the database, this is the unique identifier for that token.
- **PersonId [identifier]** – The identifier for the person the token was
    associated with
- **Data [string]** - A tokens raw binary data received from reader.

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**                ||
|------------------|----------------------------------|-|
| Success          | TokenData, TokenId, PersonId     |
| PinTimeout       | TokenData, [TokenId], [PersonId] |
| DuressPinUsed    | TokenData, TokenId, PersonId     |
| IncorrectPin     | TokenData, TokenId, PersonId     |
| UnknownToken     | TokenData                        |
| UnknownPerson    | TokenData, TokenId               |
| TokenNotEnabled  | TokenData, TokenId, PersonId     |
| PersonNotEnabled | TokenData, TokenId, PersonId     |
| ReaderDisabled   | TokenData, [TokenId], [PersonId] | Very unlikely as reader comms is disabled! |
| ReaderIsolated   | TokenData, [TokenId], [PersonId] |
| RejectedFormat   | Data                             |

>N.B. Reader Commands have the event name of `Read:n` where 'n' is the command number 1 to 9. See above for information on Reader Commands.

## Commands

### SetupDevice

Same as the `SetupDeviceAndEncryption` command.

### SetupDeviceAndEncryption

**Supported >= 4.2.0**.

This command will search for an offline device (reader) connected to the bus and change it's baud rate and address (if necessary) to the configured properties, and also set the devices encryption key. This should then bring the device online.

The reader must be set to 'install mode' before issuing this command.

If you have more than one device offline on the bus, you must define the `ScanAddresses` argument to `false` and have set-up each readers' address beforehand. Not doing this will cause unpredictable results.

 The encryption key used to set-up the reader will be based on the following order:

1. Derived from the OSDP master key `osdpMKey` defined in the blade config.
2. Provided by the `osdpDeviceKey` defined in the reader config. See above.
3. Provided in the command, using the `Key` argument.
4. Randomly created by the controller, unique to that reader. This is the preferred method, as the key is then only known to the controller and reader.

Optional parameters are:

- **Key [stringHex16] (empty)** - Overrides controller key allocation. This is not the preferred option (see above). Example "303132333435363738393A3B3C3D3E3F". **N.B. Don't use this example key!**.
- **LowestAddress [int] (0)** - The lowest address scanned.
- **HighestAddress [int] (7)** - The highest address scanned.
- **ScanAddresses [bool] (true)** - If false the `LowestAddress` and `HighestAddress` arguments are ignored and the search for the a device will only be on the defined address. The different baud rates will be scanned regardless if this setting.

Depending on the result of the command the following items will be present in the event contents.

| **Result**           | **Reason**            |
|----------------------|-----------------------|
| FailedBecauseOfError | DeviceAlreadyOnline   |
|                      | UndefinedBaudRate     |
|                      | FailedToSetConfig     |
|                      | FailedToSetComms      |
| CommandArgumentError | AddressRangeWrong     |
|                      | KeyDefinedInReaderConfig |
|                      | KeyDefinedInBladeConfig  |
