# SmartIntego.Portal

**Supported >= 2.0.0**

A portal is the logical representation of a door.  In a SmartIntego Lock a reader and portal tightly integrated as a single unit and can only work with other SmartIntego devices, and as a result can only be used with SmartIntego Readers. The hardware address of the portal is inferred from the attached readers. 

- **Normal** (Default) - Portal is normally locked. A successful entry or exit
    request will unlock the portal for a period.
- **Unlocked** - Portal is normally unlocked, allowing free access until the
    mode is changed.
- **OfficeMode** - Allows the use of a second long tap of a token to unlock the portal.
- **Disabled** - Portal is locked. Entry and exit requests will be denied.

````json
{
    "controller": {
        "portals": [{
            "id": "BoardRoom",
            "type": "SmartIntego.Portal",
            "description": "Board Room",
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
            "changeModePermissions": ["Operator"],
            "offlineWhitelistTags": ["offlineWhitelist"],
            "localWhitelistTags": ["localWhitelist"],
        }]
    }
}
````

## Properties

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the
`ChangeMode` command.

### Direction Properties (Enter & Exit)

#### readers

**[entityId[]] (empty)** The list of reader Ids that the portal should accept
identities for. A reader id can only be present in a single direction. If the
Id exists in multiple directions, then the controller will raise a *BadConfig* event.

#### accessPermissions

**[string[]] (empty)** A person with permission on this list requires no other
authorization.

### areas

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

### BatteryWarning

**[boolean]** Is the locks battery starting to run low.

### BatteryAlarm

**[boolean]** Is the locks battery almost exhausted.

### PortalLockState

**[enum] [diagnostic]** The current state of the lock.

- locked
- unlocked

### PortalBoltState

**[enum] [diagnostic]** The current state of the lock's dead bolt if installed.

- locked
- unlocked

### ExitHandlePushed

**[boolean] [diagnostic]** Has the exit handle been pushed.

### PortalSenseState

**[enum] [diagnostic]** The current state of the lock's door sensor if installed.

- open
- closed
- error
- unknown

## Events

The following events are sent from the portal. All event have the portal
identity as their event source. Each event carries extra data in its payload,
which is listed with each event.

### PortalEntryUsed/PortalExitUsed

After a Person is granted access to use a portal, they must actually use it,
i.e. open the door. This event is raised to confirm the Person has used the
portal, or signal an error if they didn’t. The event may contain the following
extra information:

- PersonId [identifier] – The unique identifier for the Person requesting to
    use the portal

| **Result**            | **Event Content** |
|-----------------------|-------------------|
| Success               | PersonId          |
| FailedMinOpenTime     | PersonId          |
| FailedPortalNotOpened | PersonId          |
| FailedNoEscort        | PersonId          |

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

### ChangeMode

Add or remove an entry from the operational mode stack of the portal.

Add Entry to stack

- **Mode [enum]** - The mode to change to.
- **Priority [int]** - The priority for the mode entry.
- **Period [timespan] (optional)** - If provided the entry will be automatically removed after the given time period.
- **Reference [string] (optional)** - A reference that can be used to remove the entry from the stack.

Remove entry from stack

- **Reference [string] (optional)** - Remove the entry with the matching reference from the stack.

Depending on the result of the command the following items will be present in the
event contents.

| **Result**           | **Reason**            |   **Event Content** |
|----------------------|-----------------------|---------------------|
| Success              |                       | [Mode]              |
| FailedOnPermissions  | NoPermisions          | [Mode]              |
|                      | NoRelevantPermissions | [Mode]              |
|                      | NoActivePermissions   | [Mode]              |
| CommandArgumentError |                       | [Mode]              |

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
