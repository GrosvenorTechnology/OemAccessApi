# Hardware.Controller

The controller type is the base of the deviceConfig.json file that will
configure the AccessControl application on the controller.

The controller has the following operational modes

- **normal** (Default)

````json
{
    "controller": {
        "id": "Ctrl-46537453278",
        "description": "Reception",
        "timezone": "Europe/London",
        "enableUserEntityRequest": false
    }
}
````

## Properties

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### timezone

**[string (200)] (Etc/UTC)** The IANA timezone to be used to evaluate local time. The list of timezones can be found in the [IANA Timezone database](https://www.iana.org/time-zones) of which a list can be found [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

### unknownTokenRequestEnabled (>= V4.0.24734)

**[bool] (false)** If set to true, the controller will make a network request to the server and request details of unknown tokens, [see here](../API/UserEntityRequest.md) for details

### unknownTokenTimeout (>= V4.0.24734)

**[timespan] (1 day)** How long to cache the unknown token result.  If the server returns a result that the token is unknown, the controller will not ask the server for the token again for the period defined in this setting.

## States

### OperationalMode

**[enum]** This shows the current controller mode.

### ExternalTamper

**[enum]** - The current state of the external tamper input (if present)

- **active**
- **reset**

### ProximityTamper

**[enum]** - The current state of the optical tamper input (if present)

- **active**
- **reset**

### ConfigButton

**[enum]** - The current state of the config button.

- **active**
- **reset**

### MainsPower

**[enum]** - The current state mains power.

- **PowerFailed**
- **PowerRestored**

### BatteryHealth

**[enum]** - The health of the battery:

- **Unknown**
- **Good**
- **Dead**
- **OverVoltage**
- **Cold**

### Ps5Amp

**[enum]** - The current state 5Amp power supply.

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description |
|------------|----------|-------------|
| Voltage    | float    | Supply voltage at time of transition |
| Current    | float    | Current draw on supply in mA |
| Status     | enum     | NotCharging, Charging, Discharging, Unknown |

### Ps8Amp

**[enum]** - The current state 8Amp power supply (if present).

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description |
|------------|----------|-------------|
| Voltage    | float    | Supply voltage at time of transition |
| Current    | float    | Current draw on supply in mA         |
| Status     | enum     | NotCharging, Charging, Discharging, Unknown |

### PsBattery

**[enum]** - The current state battery supply (if present).

- **Missing**
- **Present**

When this state changes between states, the following entries will be added to
the event contents.

| Content       | Type     | Description |
|---------------|----------|-------------|
| Voltage       | float    | Supply voltage at time of transition |
| Current       | float    | Current draw on supply in mA |
| Status        | enum     | Please see below for values |
| Online        | boolean  | Battery being used to power controller |
| BatteryHealth | enum     | Please see below for values |
| DieTemp       | int      | Temperature of charging circuit in degC |

#### Status

**[enum]** - The status of the battery:

- **Unknown**
- **NotCharging**
- **Charging**
- **Discharging**

#### BatteryHealth

**[enum]** - The health of the battery:

- **Unknown**
- **Good**
- **Dead**
- **OverVoltage**
- **Cold**

### PsPoe

**[enum]** - The current state of the Power over Ethernet supply (if present).

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description |
|------------|----------|-------------|
| Current    | float    | Current draw on supply in mA |

### PsPoePlus

**[enum]** - The current state of the Power over Ethernet Plus supply (if present).

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description |
|------------|----------|-------------|
| Current    | float    | Current draw on supply in mA |

### PsSys12Volt

**[enum]** - The current state of the the 12V system supply rail.

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description |
|------------|----------|-------------|
| Voltage    | float    | Supply voltage at time of transition |

### PsBlade12Volt

**[enum]** - The current state of the the common 12V supply for all blades.

- **Missing**
- **Present**

### PsBlade5Volt

**[enum]** - The current state of the the common 5V supply for all blades.

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description |
|------------|----------|-------------|
| Voltage    | float    | Supply voltage at time of transition |

### SshState

**[enum]** The state of the SSH service.

- **Enabled**
- **Disabled**

## Events

### ConfigurationDownload

Event is raised when application configuration is downloaded and applied.

| **Result**       | **Event Content**                |
|------------------|----------------------------------|
| Success          |                                  |
| Error            | ErrorDescription                 |

There can be one or more errors listed in the ErrorDescription. Each error is in the following format:

"Property: {PropertyPrefix}:{PropertyName}, ErrorType: {ErrorType}, Error: {ErrorDescription}"

The error types are:

- NotFound,
- NotUnique,
- BelowMin,
- AboveMax,
- NotInRange,
- Invalid

## Commands

For a command to be successful, it requires permission. There is a list of
security contexts for each command. These are linked to permissions. Providing
one comes back positive, the command is allowed.

If a command is marked is *internal,* then it is available only within the
controller but will still raise events.

### EnableSsh

This command will enable SSH.

- **Period [timespan] (optional)** – The default is 12 hours.

| **Result**    | **Reason**     | **Event Content** |
|---------------|----------------|-------------------|
| Success              |         |                   |
| FailedBecauseOfError |         |                   |
| CommandArgumentError |         |                   |

### DisableSsh

This command will disable SSH.

| **Result**    | **Reason**     | **Event Content** |
|---------------|----------------|-------------------|
| Success              |         |                   |
| FailedBecauseOfError |         |                   |

### Restart [>=4.0.0.0]

This command will restart the application running on the controller.

Added in version (N)

| **Result**    | **Reason**     | **Event Content** |
|---------------|----------------|-------------------|
| Success              |         |                   |
| FailedBecauseOfError |         |                   |

### Reboot [>=4.0.0.0]

This command will reboot the controller.

Added in version (N)

| **Result**    | **Reason**     | **Event Content** |
|---------------|----------------|-------------------|
| Success              |         |                   |
| FailedBecauseOfError |         |                   |

### EnableUsbUpdate [>=4.0.0.0]

This command will enable USB update service on the controller for 1 hour.

USB stick **MUST NOT** be larger than **4GB** and **MUST BE** formatted in **FAT32** or **FAT** format.

New firmware and / or application **must** be located inside the "firmware" folder on the root of USB.

Firmware file naming convention - "ADV10-fw-{build number}.bin" or "ADV40-fw-{build number}.bin"

Application file naming convention - "{application type}-app-{build number}.zip"

| **Result**    | **Reason**     | **Event Content** |
|---------------|----------------|-------------------|
| Success              |         |                   |
| FailedBecauseOfError |         |                   |

### DisableUsbUpdate [>=4.0.0.0]

This command will disable USB update service on the controller.

| **Result**    | **Reason**     | **Event Content** |
|---------------|----------------|-------------------|
| Success              |         |                   |
| FailedBecauseOfError |         |                   |

### EnableLogging [>=4.0.0.0 & FW >=1.1.0]

This command will enable logging service forcefully start / restart logging service.

- **Period [timespan] (optional)** – The default is 12 hours.

| **Result**    | **Reason**     | **Event Content** |
|---------------|----------------|-------------------|
| Success              |         |                   |
| FailedBecauseOfError |         |                   |
| CommandArgumentError |         |                   |

### DisableLogging [>=4.0.0.0 & FW >=1.1.0]

This command will forcefully stop logging service.

| **Result**    | **Reason**     | **Event Content** |
|---------------|----------------|-------------------|
| Success              |         |                   |
| FailedBecauseOfError |         |                   |

### SetPersonState

Set a persons current state in all areas hosted by the controller.

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       | PersonId          |
| FailedOnPermissions  | NoPermissions         | PersonId          |
|                      | NoRelevantPermissions | PersonId          |
|                      | NoActivePermissions   | PersonId          |
| CommandArgumentError |                       | [PersonId]        |
