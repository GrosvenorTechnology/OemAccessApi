# SmartIntego.Portal

**Supported >= 2.0.0**.

A portal is the logical representation of a door.  In a SmartIntego Lock a reader and portal tightly integrated as a single unit and can only work with other SmartIntego devices, and as a result can only be used with SmartIntego Readers. The hardware address of the portal is inferred from the attached readers.

- **Normal** (Default) - Portal is normally locked. A successful entry or exit
    request will unlock the portal for a period.
- **Unlocked** - Portal is normally unlocked, allowing free access until the
    mode is changed.
- **Disabled** - Portal is locked. Entry and exit requests will be denied.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

````json
{
    "controller": {
        "portals": [{
            "id": "BoardRoom",
            "type": "SmartIntego.Portal",
            "description": "Board Room",
            "address": "10-0-600",
            "operationalMode": "normal",
            "entry": {
                "readers": ["BoardRoom-Entry"],
                "accessPermissions": ["BoardRoom_Entry"],
                "areas": ["On-Site"]
            },
            "exit": {
                "readers": ["BoardRoom-Exit"],
                "permissions": ["GeneralAccess"]
            },
            "verifyInAreas": [],
            "verifyOutOfAreas": [],
            "changeModePermissions": ["Operator"],
            "offlineWhitelistTags": ["offlineWhitelist"],
            "localWhitelistTags": ["localWhitelist"],
            "unlockOnTimeTable": {
                "id": "Monday-Friday-9-17",
                "priority": 100
            },
            "unlockOnSystemMode": {
                "id": "Fire-Alarm",
                "priority": 10
            },
            "normalOnSystemMode": {
                "id": "Fire-Alarm",
                "priority": 50
            },
            "shortWakeupTimeTable": "Office-Hours",
            "officeModePermissions": ["BoardRoom_OfficeMode"],
            "officeModeDuration": "08:00:00"
        }]
    }
}

````

## Properties

### address

**[hardware-address]** The logical address for the portal.  For SmartIntego the first number is the bus number, which links a reader to a gateway, the second is always zero and the third is the device ID in **hex**.

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the
`ChangeMode` command.

### verifyInAreas

**[entityId[]] (empty)** The list of areas that are to be checked before movement through the portal is allowed. Only if they are in the area are they allowed through. This does not alter the areas' state.

### verifyOutOfAreas

**[entityId[]] (empty)** The list of areas that are to be checked before movement through the portal is allowed. Only if they are out of the area are they allowed through. This does not alter the areas' state.

### Direction Properties (Enter & Exit)

#### readers

**[entityId[]] (empty)** The list of reader Ids that the portal should accept
identities for. A reader id can only be present in a single direction. If the
Id exists in multiple directions, then the controller will raise a *BadConfig* event.

#### accessPermissions

**[string[]] (empty)** A person with permission on this list requires no other
authorization.

#### areas

**[entityId[]] (empty)** The list of areas that are to be checked before movement
through the portal is allowed. An area id can only be present in a single direction.
If the Id exists in multiple directions, then the controller will raise a *BadConfig* event.

### unlockOnTimeTable

**[object]** When set this property will add an entry to the operational mode stack for
`unlocked` with the specified priority when the specified TimeTable is active.

````json
"unlockOnTimeTable": {
    "id": "Monday-Friday-9-17",
    "priority": 100
}
````

### unlockOnSystemMode

**[object]** When set this setting will add an entry to the operational mode stack for
`unlock` with the specified priority when the specified `SystemMode` is active.

````json
"unlockOnSystemMode": {
    "id": "Fire-Alarm",
    "priority": 10
}
````

### normalOnSystemMode

**[object]** When set this setting will add an entry to the operational mode stack for
`normal` with the specified priority when the specified `SystemMode` is active.

````json
"normalOnSystemMode": {
    "id": "Fire-Alarm",
    "priority": 50
}
````

### shortWakeupTimeTable

**[[Common.TimeTable](CommonTimeTable.md)]** When set the Smart Intego lock will be set to short wake-up time when the specified TimeTable is active.

### officeModePermissions

**[string[]] (empty)** A person with a permission on this list will, provided they also have access permission, be allowed put the Smart Intego lock in and out of Office Mode.

### officeModeDuration

**[timespan] (04:00:00)** This will determine for how long Office Mode will last.

## States

### OperationalMode

**[enum]** This shows the current portal mode (see Portal Operational Modes for
details).

### PortalState

**[enum]** The state shows the current state of the portal.

- LockedShut
- UnlockShut
- Open
- OpenTooLong - When the portal enters this state it will include the `PersonId` in the event content
- OpenForced
- LockedNoSensor
- UnlockedNoSensor

### BatteryAlarm

**[boolean]** Is the locks battery almost exhausted.

### BatteryWarning

**[boolean]** Is the locks battery starting to run low.

### EscapeReturnActive

**[boolean] [diagnostic]** The current state of the lock Escape/Return function.

### ExitHandlePushed

**[boolean] [diagnostic]** Has the exit handle been pushed.

### PortalBoltState

**[enum] [diagnostic]** The current state of the lock's dead bolt if installed.

- locked
- unlocked

### PortalLockState

**[enum] [diagnostic]** The current state of the lock.

- locked
- unlocked

### PortalSenseState

**[enum] [diagnostic]** The current state of the lock's door sensor if installed.

- open
- closed
- error
- unknown

### QualityOfService

**[enum] [diagnostic]** The current Quality of Service of the lock communications.

- QoS80
- QoS90
- QoS95

### ReaderConnected

**[boolean] [diagnostic]** Whether a reader is connected to the lock.

### WhitelistDeactivated

**[boolean]** Whether the lock's white list is active.

## Events

The following events are sent from the portal. All event have the portal
identity as their event source. Each event carries extra data in its payload, which is listed with each event.

### PortalEntryUsed/PortalExitUsed

After a Person is granted access to use a portal, they must actually use it,
i.e. open the door. This event is raised to confirm the Person has used the
portal, or signal an error if they didn't. The event may contain the following extra information:

- PersonId [identifier] – The unique identifier for the Person requesting to
    use the portal

| **Result**            | **Event Content** |
|-----------------------|-------------------|
| Success               | PersonId, TokenId, TokenData, ReaderId |
| FailedMinOpenTime     | PersonId, TokenId, TokenData, ReaderId |
| FailedPortalNotOpened | PersonId, TokenId, TokenData, ReaderId |
| FailedNoEscort        | PersonId, TokenId, TokenData, ReaderId |

### PortalUsed

If the Portal has a request to exit switch, then when this is used this event
will be raised.

| **Result**            | **Event Content** |
|-----------------------|-------------------|
| Success               |                   |
| FailedMinOpenTime     |                   |
| FailedPortalNotOpened |                   |

### MagneticManipulation

Caused by a magnetic manipulation of the lock.

### NoHallSensors

Caused by the lock's Hall sensor not being found.

### ShortCircuit

Caused by the lock's sensor being short circuit.

### HandleManipulation

Caused by a handle manipulation of the lock.

### OfflineWhitelistAccesses

The number of accesses granted while the lock was off-line, allowed because the tokens were on the whitelist.

| **Result**            | **Event Content** |
|-----------------------|-------------------|
| [int]                 |                   |

## Commands

For a command to be successful, it requires permission. There is a list of
security contexts for each command. These are linked to permissions. Providing
one comes back positive, the command is allowed.

If a command is marked is *internal,* then it is available only within the
controller but will still raise events.

### RequestEntry/RequestExit [internal]

This command requests entry through the portal. Several checks are made before the
request is allowed, they are:

- Whether the portal can accept the command e.g. not disabled or prevented due to portal interlock.
- Whether the person has permission.
- Whether the person passes all area APB checks associated with the portal.

- **PersonId [identifier]** – The unique identifier for the Person requesting to
    use the portal

| **Result**          | **Reason**            | **Event Content** |
|---------------------|-----------------------|-------------------|
| Success             |                       | PersonId          |
| FailedOnPermissions | FailedAreaVerify      | PersonId          |
|                     | FailedAreaRule        | PersonId          |
|                     | NoPermissions         | PersonId          |
|                     | NoRelevantPermissions | PersonId          |
|                     | NoActivePermissions   | PersonId          |
|                     | PortalDisabled        | PersonId          |

### SingleUnlock

The portal will been commanded to unlock e.g. via interactive user command. The
command will either result in a success or failure error with an associated
reason.

| **Result**          | **Reason**            | **Event Content** |
|---------------------|-----------------------|-------------------|
| Success             |                       |                   |
| FailedOnPermissions | NoPermissions         |                   |
|                     | NoRelevantPermissions |                   |
|                     | NoActivePermissions   |                   |
|                     | PortalDisabled        |                   |

### ActivateWhitelist

Activates white list.

| **Result**           |   **Event Content** |
|----------------------|---------------------|
| Success              |                     |
| FailedBecauseOfError |                     |

### DeactivateWhitelist

Deactivates white list.

| **Result**           |   **Event Content** |
|----------------------|---------------------|
| Success              |                     |
| FailedBecauseOfError |                     |

### UpdateWhitelist

Forces white list to be re-evaluated and changes sent to lock.

| **Result**           |   **Event Content** |
|----------------------|---------------------|
| Success              |                     |
| FailedBecauseOfError |                     |

### SendWhitelist

Forces white list to be cleared and re-sent sent to lock.

| **Result**           |   **Event Content** |
|----------------------|---------------------|
| Success              |                     |
| FailedBecauseOfError |                     |

### DeleteWhitelist

Forces white list to be cleared.

| **Result**           |   **Event Content** |
|----------------------|---------------------|
| Success              |                     |
| FailedBecauseOfError |                     |

### DeletePriorityWhitelist

Forces priority white list to be cleared.

| **Result**           |   **Event Content** |
|----------------------|---------------------|
| Success              |                     |
| FailedBecauseOfError |                     |
