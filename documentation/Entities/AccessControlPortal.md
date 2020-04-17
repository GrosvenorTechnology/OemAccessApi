# AccessControl.Portal

A Portal most commonly defines a door, but may be a car park entry barrier or a
lock on a storage container. A portal is directional, it has an entry and exit
direction, corresponding typically to both sides of a door. When the port is
only being used to validate entry into an area and has an exit switch for exit,
the exit settings can be omitted.

When a Portal is used in conjunction with Areas, the portal will check that the
user requesting access is in the correct areas before allowing access.

The portal has the following operational modes

- **Normal** (Default) - Portal is normally locked. A successful entry or exit
    request will unlock the portal for a period.
- **Unlocked** - Portal is normally unlocked, allowing free access until the
    mode is changed.
- **FirstPersonUnlock** - Portal, when first entering this operational mode,
    is locked. A successful entry or exit request will unlock the portal,
    allowing free access until the mode is changed.
- **Disabled** - Portal is locked. Entry and exit requests will be denied.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

````json
{
    "controller": {
        "portals": [{
            "id": "Reception-1",
            "description": "Reception Front Door",
            "address": "1-0-1",
            "operationalMode": "normal",
            "monitorForForcedEntry": true,
            "lockType": "energiseToLock",
            "lockCurrentLimit": 4000,
            "lockWarningCurrentMinimum": 20,
            "lockWarningCurrentMaximum": 3000,
            "useAuxOutputForLock": false,
            "relockMode": "onPortalOpen",
            "sensorType": "disabled",
            "switchType": "unsupervised",
            "breakGlass": "disabled",
            "normalUnlockPeriod": "00:00:05",
            "extendedUnlockPeriod": "00:00:10",
            "normalMinimumOpenPeriod": "00:00:02",
            "extendedMinimumOpenPeriod": "00:00:04",
            "normalOpenTooLongPeriod": "00:00:30",
            "extendedOpenTooLongPeriod": "00:01:00",
            "forcedSounderMode": "urgentPulse",
            "forcedSounderPeriod": "00:00:20",
            "openTooLongSounderMode": "nonUrgentPulse",
            "openTooLongSounderPeriod": "00:00:10",
            "unlockOnTimeTable": {
                "id": "Monday-Friday-9-17",
                "priority": 100
            },
            "unlockOnSystemMode": {
                "id": "Fire-Alarm",
                "priority": 10
            },
            "normalOnSystemMode": {
                "id": "Lock-Doors",
                "priority": 50
            },
            "entry": {
                "readers": ["Reception-External"],
                "accessPermissions": ["GeneralAccess"],
                "escortedPermissions": ["EscortedUsers"],
                "escortPermissions": ["Escort"],
                "areas": ["6F02B316-06A6-4886-A2BD-89B18122F222"]
            },
            "exit": {
                "readers": [
                    "Reception-Internal-Left",
                    "Reception-Internal-Right"],
                "permissions": ["GeneralAccess","Reception-Exit"]
            },
            "verifyInAreas": [],
            "verifyOutOfAreas": [],
            "singleUnlockPermission": ["Operator"],
            "changeModePermissions": ["Operator"]
        }]
    }
}
````

## Properties

### address

**[hardware-address] [required]** The controller logical address for the portal

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### monitorForForcedEntry

**[Boolean] (false)** When set, the `PortalState` will go to `OpenForced` when a portal is opened without the portal being unlocked. Otherwise, the `PortalState` will go to `Open` and eventually `OpenTooLong`.

N.B. An exit switch or reader is required at each side of the portal, if monitoring for forced entry is required.

### lockType

**[enum] (energiseToLock)** The type of lock attached.

- energiseToLock
- energiseToUnlock

### lockDetectionType

**[enum] (disable)** There are two ways of detecting that a lock is connected. The best to use often depends on the `lockType`.

- disable
- openCircuit
- lockCurrent

### lockCurrentLimit

**[int] (4000)** The lock current limit at which power to the lock will be cut.

### lockWarningCurrentMinimum

**[int] (20)** The lock current at which a lock missing event will be raised.

### lockWarningCurrentMaximum

**[int] (3000)** The lock current at which an over current warning event will be raised

### useAuxOutputForLock

**[boolean] (false)** If set to true, when a look it energised the matching aux output
will also be operated, i.e. lock 1 drives output 1.

### auxOutputForLockOutputType

**[enum] (activeHigh)** This allows you to define whether the 'contacts' close (activeHigh) or open (activeLow) when operated.

- activeLow
- activeHigh

### relockMode

**[enum] (onPortalOpen)** When should the portal be relocked, only used if the sensor is enabled.

- onPortalOpen
- onPortalClose

### sensorType

**[enum] (disabled)** Portal Sensor type.

- disabled
- unsupervised
- supervised

### switchType

**[enum] (unsupervised)** Portal egress switch type.

- disabled
- unsupervised
- supervised

### breakGlass

**[enum] (disabled)** Configure if a break glass is present.

- disabled
- unsupervised

### portalInterlock

**[entityId] (empty)** If the portal belongs to a portal interlock, specify the portal interlock id here.

### normalUnlockPeriod

**[timespan] (00:00:05)** The normal period a portal is unlocked for when a request for entry
or exit is successful. This can be cut short by relock mode.

### extendedUnlockPeriod

**[timespan] (00:00:10)** The extended period a portal is unlocked for when a request for entry
or exit is successful. Used when a person has the ‘Wheelchair or impaired
mobility’ attribute. This can be cut short by relock mode.

### normalMinimumOpenPeriod

**[timespan] (00:00:02)** The normal minimum period that a portal is open for, that a person
can be considered to have gone through.

### extendedMinimumOpenPeriod

**[timespan] (00:00:04)** The minimum period that a portal is open for, that a person with the
‘Wheelchair or impaired mobility’ attribute can be considered to have gone
through.

### normalOpenTooLongPeriod

**[timespan] (00:00:30)** The normal period a portal can be open for before an alarm is raised.

### extendedOpenTooLongPeriod

**[timespan] (00:01:00)** The period a portal can be open for before an alarm is raised when
used by a person with the ‘Wheelchair or impaired mobility’ attribute.

### forcedSounderMode

**[enum] (urgentPulse)** Configure the sounder mode for when the portal is forced open.

- disabled
- constant
- urgentPulse
- nonUrgentPulse
- reminderPulse

### forcedSounderPeriod

**[timespan]** How long to activate the sounder when the portal is forced.

### openTooLongSounderMode

**[enum] (nonUrgentPulse)** Sets the sounder mode for when the portal is open to long.

- disabled
- constant
- urgentPulse
- nonUrgentPulse
- reminderPulse

### openTooLongSounderPeriod

**[timespan] (00:00:10)** How long to activate the sounder when the portal is held open.

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

### singleUnlockPermission

**[string[]] (empty)** A person with a permission on this list can execute the
`SingleUnlock` command.

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

#### escortedPermissions

**[string[]] (empty)** A person with permission on this list requires further
authorization by another person who has Escort permission. The reader will be
informed another read is required.

#### escortPermissions

**[string[]] (empty)** A person with permission on this list can authorise the
Escorted request. If the escort has also permission on the access list, the request
is granted, and both deemed to enter.

If the escort only has permission on the escorted list, and the escorted person
has escort permission, the request is granted, and both deemed to enter. i.e.
they are escorting each other.

One other scenario exists where the escort has no other permissions. The read
will be regarded as an authorization for the escorted read, and only the
escorted person will be deemed to enter. i.e. A guard on the door has used a
token set-up only to authorize.

#### areas

**[entityId[]] (empty)** The list of areas that are to be checked before movement
through the portal is allowed. An area id can only be present in a single direction.
If the Id exists in multiple directions, then the controller will raise a *BadConfig* event.

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

### BreakGlassState

**[enum]** The current state of the break glass input.

- inactive
- active
- error
- unknown

### LockDetectionState

**[enum]** Reports if a lock is detected.

- missing
- present
- unknown

### LockCurrentLimitState

**[enum]** Reports if a lock has exceeded it's current limit.

- tripped
- cleared

### LockPowerState

**[enum]** Reports if the voltage of the lock power supply is within warning limits.

- normal
- aboveMaxWarning
- belowMinWarning

### ConnectedToBlade

**[boolean] [diagnostic]** Is the portal available on a connected blade.

### portalLockState

**[enum] [diagnostic]** State of the portal's lock.

- locked
- unlocked

### egressSwitchState

**[enum] [diagnostic]** State of the egress switch.

- active
- inactive
- error
- unknown

### egressSwitchErrorState

**[enum]** Error state of the egress switch.

- Unknown
- Ok
- Error

## Events

The following events are sent from the portal. All event have the portal
identity as their event source. Each event carries extra data in its payload,
which is listed with each event.

### PortalEntryUsed/PortalExitUsed

After a Person is granted access to use a portal, they must actually use it,
i.e. open the door. This event is raised to confirm the Person has used the
portal, or signal an error if they didn't. The event may contain the following
extra information:

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

## Commands

For a command to be successful, it requires permission. There is a list of
security contexts for each command. These are linked to permissions. Providing
one comes back positive, the command is allowed.

If a command is marked is *internal,* then it is available only within the
controller but will still raise events.

### RequestEntry/RequestExit [internal]

This command requests entry through the portal. Several checks are made before the
request is allowed, they are:

- Whether the portal can accept the command e.g. not disabled or prevented due
  to portal interlock.
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

### PulseSounder

The portal will been commanded to activate the sounder associated to it e.g. via
interactive user command. The command will either result in a success or failure
error with an associated reason.

| **Result**          | **Reason**            | **Event Content** |
|---------------------|-----------------------|-------------------|
| Success             |                       |                   |
| FailedOnPermissions | NoPermissions         |                   |
|                     | NoRelevantPermissions |                   |
|                     | NoActivePermissions   |                   |
