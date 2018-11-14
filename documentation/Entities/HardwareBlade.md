# Hardware.Blade

**Beta >= 3.0.0**
**Supported >= 3.2.0**

The configuration of the blades can be changed from the defaults by adding a section in the Application Configuration file. The addresses of the blades are defined as '1-0-1', '2-0-1', '3-0-1' and '4-0-1'. The properties of a blade will be determined by the blade type. Currently there are 'Door' and 'I/O' blade types.

The blade has the following operational modes:

- **normal** (Default)

````json
{
    "controller": {
        "blades": [{
                "id": "blade-1",
                "address": "1-0-1",
                "v12CurrentLimit": 2000,
                "v12CurrentMinimum": 2,
                "v12CurrentMaximum": 1900,
                "lockPowerSource": "auto",
                "lockVoltageMinimum": 11000,
                "lockVoltageMaximum": 14000,
                "osdpBaudRate": "baud9600",
                "osdpMKey": ""
            }
        ]
    }
}
````

## Properties

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### address

**[hardware-address] [required]** The controller logical address for the input

### V12CurrentLimit - Door blade only

**[int] (2000)** This sets the maximum current (defined in mAmps) allowed to be drawn from the V12 supply. Above this level the supply will trip and not be restored until the load is reduced.

### V12CurrentMinimum - Door blade only

**[int] (2)** This sets the minimum current (defined in mAmps) that is expected to be drawn from the V12 supply. Any less than this will cause the V12InCurrent state to change to 'belowMinWarning'.

### V12CurrentMaximum - Door blade only

**[int] (1900)** This sets the maximum current (defined in mAmps) that is expected to be drawn from the V12 supply. Any more than this will cause the V12InCurrent state to change to 'aboveMaxWarning'.

### LockPowerSource - Door blade only

**[enum] (auto)** This defines the lock's power source. The 'auto' setting will get the blade to test for an external lock power supply on blade power-up. If it is found, the external power supply is used.
N.B. Putting external lock power on the blade after power-up when the setting is 'auto', will NOT allow the blade to use the external lock supply. Valid options are:

- **internal**
- **external**
- **auto**

### LockVoltageMinimum - Door blade only

**[int] (11000)** This sets the minimum voltage (defined in mVolts) that is expected on the lock power supply. Any less than this will cause the LocksVoltage state to change to 'belowMinWarning'.

### LockVoltageMaximum - Door blade only

**[int] (14000)** This sets the maximum voltage (defined in mVolts) that is expected on the lock power supply. Any more than this will cause the LocksVoltage state to change to 'aboveMaxWarning'.

### OsdpBaudRate - Door blade only

**[enum] (baudDisabled)** This defines the OSDP baud rate for both OSDP ports on the blade. Valid options are:

- **baudDisabled**
- **baud9600**
- **baud19200**
- **baud38400**
- **baud115200**

### OsdpMKey - Door blade only

**[string] ('')** This defines the encryption key to use to encrypt the OSDP line. As empty string signifies that no encryption is to be used.

### OsdpTerminate - Door blade only

**[bool] (false)** Set to true to use the blades internal line terminating resistor.

### OsdpBusStats - Door blade only

**[bool] (true)** If true the OSDP devices get polling statistics and save them as diagnostic states.

## States

### OperationalMode

**[enum]** This shows the current operational mode.

### ConnectedToBlade

**[boolean] [diagnostic]** Is the input available on a connected blade.

### BladePartNumber

**[string]** The part code of the blade.

### BladeHardwareRevision

**[string]** The blade hardware revision.

### BladeFirmwareType

**[string]** The blade firmware type.

### BladeFirmwareVersion

**[string]** The blade firmware version.

### V12CurrentLimitState

**[enum]** Has the 12V current limit been tripped.

- **tripped**
- **cleared**

### V12OutCurrentLimitState

**[enum]** Has the 12V current limit been tripped.  TODO: What's the difference between this and V12CurrentLimitState?

- **tripped**
- **cleared**

### V12VInVoltage

**[enum]** State of the 12V rail voltage to the blade

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V12InCurrent

**[enum]** State of the 12V rail current being used by the blade

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### LocksVoltage

**[enum]** State of the Lock power input for the blade

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5RdrSupply

**[enum]** State of the 5V supply for the readers

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5ExtSupply

**[enum]** TODO: description needed!

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5Ext1Supply

**[enum]** TODO: description needed!

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5Ext2Supply

**[enum]** TODO: description needed!

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

### V5UsbSupply

**[enum]** TODO: description needed!

- **normal**
- **aboveMaxWarning**
- **belowMinWarning**

## Events

### FirmwareVersion

At blade start-up, reports on the state of the blade firmware.

- **FirmwareVersion [string]** – The current firmware version.
- **NewFirmwareVersion [string]** – The current firmware version.
- **OldFirmwareVersion [string]** – The version the firmware will be updated to.

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**                      |
|------------------|----------------------------------------|
| Correct          | FirmwareVersion                        |
| Wrong            | NewFirmwareVersion, OldFirmwareVersion |

### FirmwareUpload

The blade firmware update progress.

- **FirmwareVersion [string]** – The current firmware version being updated to.

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**                      |
|------------------|----------------------------------------|
| Started          | FirmwareVersion                        |
| Success          | FirmwareVersion                        |
| Failed           | FirmwareVersion                        |

### BladeFirmware

The blade is not a known type.

- **FirmwareType [string]** – The firmware type detected in the blade.

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**     |
|------------------|-----------------------|
| UnknownType      | FirmwareType          |

## Commands

### Reset

Reset the blade.

Depending on the result of the command the following items will be present in the
event contents.

| **Result**      | **Reason** |   **Event Content** |
|-----------------|------------|---------------------|
| Success         |            |                     |
| FailedOnPermissions | NoPermissions |              |
|                     | NoRelevantPermissions |      |
|                     | NoActivePermissions   |      |
