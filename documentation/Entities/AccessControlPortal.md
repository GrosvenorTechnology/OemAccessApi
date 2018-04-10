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

````json
{
    "controller": {
        "portals": [{
            "id": "Reception-1",
            "description": "Reception Front Door",
            "address": "1-0-1",          
            "operationalMode": "normal",
            "requestExitEnabled": true,
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
            "openTooLongSouderPeriod": "00:00:10",
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
            "singleUnlockPermission": ["Operator"],
            "changeModePermissions": ["Operator"]
        }]
    }
}
````

## Properties

### address

**[hardware-address]** The controller logical address for the portal

### requestExitEnabled

**[Boolean]** Exit switch is enabled.

### lockType

**[enum]** The type of lock attached.

- EnergiseToLock
- EnergiseToUnlock
- UseAuxOutput

### relockMode

**[enum]** When should the portal be relocked, only used if the sensor is enabled.

- OnPortalOpening (default)
- OnPortalClosing

### sensorType

**[enum]** Portal Sensor type.

- Disabled (default)
- Unsupervised
- Supervised

### breakGlass

**[enum]** Configure if a break glass is present.

- Disabled (default)
- Unsupervised

### normalUnlockPeriod 

**[timespan]** The normal period a portal is unlocked for when a request for entry
or exit is successful. This can be cut short by relock mode. Default is 5
seconds.

### extendedUnlockPeriod

**[timespan]** The extended period a portal is unlocked for when a request for entry
or exit is successful. Used when a person has the ‘Wheelchair or impaired
mobility’ attribute. This can be cut short by relock mode. Default is 10
seconds.

### normalMinimumOpenPeriod

**[timespan]** The normal minimum period that a portal is open for, that a person
can be considered to have gone through. Default is 2 seconds.

### extendedMinimumOpenPeriod

**[timespan]** The minimum period that a portal is open for, that a person with the
‘Wheelchair or impaired mobility’ attribute can be considered to have gone
through. Default is 4 seconds.

### normalOpenTooLongPeriod

**[timespan]** The normal period a portal can be open for before an alarm is raised.
Default is 30 seconds.

### extendedOpenTooLongPeriod

**[timespan]** The period a portal can be open for before an alarm is raised when
used by a person with the ‘Wheelchair or impaired mobility’ attribute. Default
is 60 seconds.

### forcedSounderMode

**[enum]** Configure the sounder mode for when the portal is forced open.

- Disabled
- Constant
- UrgentPulse (default)
- NonUrgentPulse
- ReminderPulse

### forcedSounderPeriod

**[timespan]** How long to activate the sounder when the portal is forced. Default
is 20 seconds.

### openTooLongSounderMode

[enum] Sets the sounder mode for when the portal is open to long.

- Disabled
- Constant
- UrgentPulse
- NonUrgentPulse (default)
- ReminderPulse

### openTooLongSouderPeriod

**[timespan]** How long to activate the sounder when the portal is held open.
Default is 10 seconds.

### Direction Properties (Enter & Exit)

#### readers

The list of reader Ids that the portal should accept identities for. A reader id
can only be present in a single direction. If the Id exists in multiple
directions, then the controller will raise a *BadConfig* event.

#### accessPermissions

A person with permission on this list requires no other authorisation.

#### escortedPermissions

A person with permission on this list requires further authorisation by another
person who has Escort permission. The reader will be informed another read is
required.

#### escortPermissions

A person with permission on this list can authorise the Escorted request. If the
escort has also permission on the access list, the request is granted, and both
deemed to enter.

If the escort only has permission on the escorted list, and the escorted person
has escort permission, the request is granted, and both deemed to enter. i.e.
they are escorting each other.

One other scenario exists where the escort has no other permissions. The read
will be regarded as an authorisation for the escorted read, and only the
escorted person will be deemed to enter. i.e. A guard on the door has used a
token setup only to authorise.

### areas

The list of areas that are to be checked before movement through the portal is
allowed. An area id can only be present in a single direction. If the Id exists
in multiple directions, then the controller will raise a *BadConfig* event.

## States

### OperationalMode

**[enum]** This shows the current reader mode (see Portal Operational Modes for
details).

### ConnectedToBlade

**[boolean] [diagnostic]** Is the portal available on a connected blade.

### PortalState

**[enum]** The state shows the current state of the portal.

- LockedShut
- UnlockShut
- Open
- OpenTooLong
- OpenForced
- LockedNoSensor
- UnlockedNoSensor

### BreakglassState

**[enum]** The current state of the break glass input.

- Inactive
- Active
- Error
- Unknown

### LockDetectionState

**[enum]** Reports if a lock is detected.

- Missing
- Present
- Unknown

### LockCurrentLimitState

**[enum]** Reports if a lock has exceeded it’s current limit.

- Tripped
- Cleared

LockPowerState

### LockPowerState

**[enum]** Reports if the voltage of the lock power supply is within warning limits.

- Normal
- AboveMaxWarning
- BelowMinWarning

## Events

The following events are sent from the portal. All event have the portal
identity as their event source. Each event carries extra data in its payload,
which is listed with each event.

### ChangeMode

A request was made to change the operational mode of the portal.

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       | OperationalMode   |
| FailedOnPermissions  | NoPermisions          | OperationalMode   |
|                      | NoRelevantPermissions | OperationalMode   |
|                      | NoActivePermissions   | OperationalMode   |
| CommandArgumentError |                       | OperationalMode   |

### RequestEntry/RequestExit

A Person identified by a read has request permission to use the portal in a
specific direction (Entry or Exit). This request has a success or failure result
with an associated reason. As well as the result, the event payload will contain

- Reason [enum] – The reason for the success or failure
- PersonId [identifier] – The unique identitifier for the Person requesting to
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

### PortalEntryUsed/PortalExitUsed

After a Person is granted access to use a portal, they must actually use it,
i.e. open the door. This event is raised to confirm the Person has used the
portal, or signal an error if they didn’t. The event may contain the following
extra information:

- PersonId [identifier] – The unique identitifier for the Person requesting to
    use the portal

| **Result**            | **Reason** | **Event Content** |
|-----------------------|------------|-------------------|
| Success               |            | PersonId          |
| FailedMinOpenTime     |            | PersonId          |
| FailedPortalNotOpened |            | PersonId          |
| FailedNoEscort        |            | PersonId          |

### PortalUsed

If the Portal has a request to exit switch, then when this is used this event
will be raised.

| **Result**            | **Reason** | **Event Content** |
|-----------------------|------------|-------------------|
| Success               |            |                   |
| FailedMinOpenTime     |            |                   |
| FailedPortalNotOpened |            |                   |

### SingleUnlock

The portal has been commanded to unlock e.g. via interactive user command. The
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

The portal has been commanded to activate the sounder associated to it e.g. via
interactive user command. The command will either result in a success or failure
error with an associated reason.

| **Result**          | **Reason**            | **Event Content** |
|---------------------|-----------------------|-------------------|
| Success             |                       |                   |
| FailedOnPermissions | NoPermissions         |                   |
|                     | NoRelevantPermissions |                   |
|                     | NoActivePermissions   |                   |

## Commands

For a command to be successful, it requires permission. There is a list of
security contexts for each command. These are linked to permissions. Providing
one comes back positive, the command is allowed.

If a command is marked is *internal,* then it is available only within the
controller.

### ChangeOperationalMode

This command is used to request a change the operational mode or cancel an
existing command. All command requests are put on a list. The operational mode
is set to the command with the highest priority at any one time. If a command
times-out or is cancelled, it is taken off the list and the mode is revaluated.
With no commands on the list the mode goes to the default (Normal). To cancel a
command only the reference argument should be supplied.

Command Arguments:

- Mode – Required mode
- Period – Period that it lasts (optional)
- Priority – 0 to 255, 0 being the highest
- ReferenceId – Controlling Entity

### RequestEntry

**[internal]** This command requests entry through the portal. Several checks are
made before the request is allowed, they are:

- Whether the portal can accept the command e.g. not disabled or prevented due
    to portal interlock.
- Whether the person has permission.
- Whether the person passes all area APB checks associated with the portal.

Command Arguments:

- PersonId
- PermissionOverrideId (optional)