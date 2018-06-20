# Hardware.Controller

The controller type is the base of the deviceConfig.json file that will
configure the AccessControl application on the controller.

The controller has the following operational modes

- **Normal** (Default)

````json
{
    "controller": {
        "id": "Ctrl-46537453278",
        "description": "Reception",
        "timezone": "Europe/London",
        "areaMode": "local"
    }
}
````

## Properties

### Timezone

**[string (200)] (Etc/UTC)** The IANA timezone to be used to evaluate local time.
The list of timezones can be found in the [IANA Timezone database](https://www.iana.org/time-zones)
of which a list can be found [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

### AreaMode

**[enum] (local)** Sets the mode for area processing. Acceptable values are:

- **local** – All area processing is handled locally on the controller;
    the server is responsible for distributing area state to all controllers.
- **remote** – All error movement will be validated on the server. All local
    functions such as max occupancy will be disabled.

## States

### OperationalMode

**[enum]** This shows the current portal mode (see Portal Operational Modes for
details).

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

### Ps5Amp

**[enum]** - The current state 5Amp power supply.

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description                                 |
|------------|----------|---------------------------------------------|
| Voltage    | float    | Supply voltage at time of transition        |
| Current    | float    | Current draw on supply in mA                |
| Status     | enum     | NotCharging, Charging, Discharging, Unknown |

### Ps8Amp

**[enum]** - The current state 8Amp power supply (if present).

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description                                 |
|------------|----------|---------------------------------------------|
| Voltage    | float    | Supply voltage at time of transition        |
| Current    | float    | Current draw on supply in mA                |
| Status     | enum     | NotCharging, Charging, Discharging, Unknown |

### PsBattery

**[enum]** - The current state battery supply (if present).

- **Missing**
- **Present**

When this state changes between states, the following entries will be added to
the event contents.

| Content       | Type     | Description                                 |
|---------------|----------|---------------------------------------------|
| Voltage       | float    | Supply voltage at time of transition        |
| Current       | float    | Current draw on supply in mA                |
| Status        | enum     | NotCharging, Charging, Discharging, Unknown |
| Online        | boolean  | TODO: What does this mean?                  |
| BatteryHealth | enum     | TODO: What are the values?                  |
| DieTemp       | int      | Temperature of charging circuit in degC     |

### PsPoe

**[enum]** - The current state of the Power over Ethernet supply (if present).

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description                                 |
|------------|----------|---------------------------------------------|
| Current    | float    | Current draw on supply in mA                |

### PsPoePlus

**[enum]** - The current state of the Power over Ethernet Plus supply (if present).

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description                                 |
|------------|----------|---------------------------------------------|
| Current    | float    | Current draw on supply in mA                |

### PsSys12Volt

**[enum]** - The current state of the the 12V system supply rail.

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description                                 |
|------------|----------|---------------------------------------------|
| Voltage    | float    | Supply voltage at time of transition        |

### PsBlade12Volt

**[enum]** - The current state of the the common 12V supply for all blades.

- **Missing**
- **Present**

### PsBlade5Volt

**[enum]** - The current state of the the common 5V supply for all blades.

- **Missing**
- **Present**

When this state changes state, the following entries will be added to the event contents

| Content    | Type     | Description                                 |
|------------|----------|---------------------------------------------|
| Voltage    | float    | Supply voltage at time of transition        |

## Events

## Commands

For a command to be successful, it requires permission. There is a list of
security contexts for each command. These are linked to permissions. Providing
one comes back positive, the command is allowed.

If a command is marked is *internal,* then it is available only within the
controller but will still raise events.

### ChangeMode

Add or remove an entry from the operational mode stack of the portal.

Add Entry to stack

- **Mode [entityId]** - The mode to change to.
- **Priority [int]** - The priority for the mode entry.
- **Period [timespan] (optional)** - If provided the entry will be automatically removed after the given time period.
- **Reference [string] (optional)** - A reference that can be used to remove the entry from the stack.

Remove entry from stack

- **Reference [string] (optional)** - Remove the entry with the matching reference from the stack.

| **Result**           | **Reason**            |   **Event Content** |
|----------------------|-----------------------|---------------------|
| Success              |                       | [Mode]              |
| FailedOnPermissions  | NoPermissions          | [Mode]              |
|                      | NoRelevantPermissions | [Mode]              |
|                      | NoActivePermissions   | [Mode]              |
| CommandArgumentError |                       | [Mode]              |