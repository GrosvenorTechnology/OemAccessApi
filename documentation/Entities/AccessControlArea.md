# AccessControl.Area

An Area is the logical representation of a controlled area. Portals are the
normal entry and exit points of an area, though commands can also be used. The
area will keep a list of people known to be in or out of the area. People not
known to the area will be set to their relevant state as soon as the area deals
with them.

Areas can work over more than one controller. To allow for this a multi-master
concept has been adopted. When a change happens, the originating area instance
is responsible for creating the event and sending state, other area instances
then set their state without repeating the event or state message. This should
keep the instances in-sync. There will normally be an area instance in a host
service. An instance can ask for a re-sync, which would normally be provided by
the host service.

An area can be commanded to be in a operational mode. The default mode is set in
the area configuration.

Whatever the mode, the area will keep account of who is in or out of the area.
Restrictions on occupancy limits will also always apply when in ‘Enforce
Occupancy Limits’ state, though certain people can be made exempt.

The area has the following operational modes:

- **Unenforced** - Will not deny entry or exit, if found to be already in that
    state.
- **Enforced -** Will deny entry or exit, if found to be already in that
    state. Certain people can be made exempt.
- **LockDown -** Will deny entry or exit. Certain people can be made exempt.
- **PreventEntry -** Will deny entry, but not exit. Certain people can be made
    exempt.
- **PreventExit -** Will deny exit, but not entry. Certain people can be made
    exempt.

````json
{
    "controller": {
        "areas": [{
            "id": "6F02B316-06A6-4886-A2BD-89B18122F222",
            "description": "Area 1",
            "operationalMode": "enforced",
            "offlineMode": "unenforced",
            "enforceOccupancyLimits": true,
            "maximumOccupancy": 1000000,
            "minimumOccupancy": 1,
            "changeModePermissions": [ ],
            "setPersonStatePermission": [ ],
            "makeAllUnknownPermission": [ ],
            "makeAllOutPermissions": [ ]
        }]
    }
}
````

## Properties

### operationalMode

**[enum]** Specifies which operational mode is the default.

### offlineMode

**[enum]** Specifies which mode is used when the controller is off-line.

### enforceOccupancyLimits

**[Boolean]** If set to true occupancy limits will be enforced.

### maximumOccupancy

**[int]** The maximum number of people allowed in the area, when
enforceOccupancyLimits is set to true.

### minimumOccupancy

**[int]** The minimum number of people allowed in the area, when
enforceOccupancyLimits is set to true.

### changeModePermissions

**[string[]]** The list of permissions that allow the area mode to be changed.

### setPersonStatePermission

**[string[]]** The list of permissions that allow the state of an individual
user to be changed.

### makeAllUnknownPermission

**[string[]]** The list of permissions that allow all users in an area to be
made unknown.

### makeAllOutPermissions

**[string[]]** The list of permissions that allow all users in an area to be
marked as out of the area.

## States

### OperationalMode

**[enum]** This shows the current area mode (see Area Operational Modes for
details).

### Occupancy

**[int]** Shows the number of people in the area..

### OccupancyState

**[enum]** Shows one of the following:

- BelowMinimum
- AtMinimum
- OK
- AtMaximum
- AboveMaximum

## Events

### ChangeMode

A request was made to change the operational mode of the reader.

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       | OperationalMode   |
| FailedOnPermissions  | NoPermisions          | OperationalMode   |
|                      | NoRelevantPermissions | OperationalMode   |
|                      | NoActivePermissions   | OperationalMode   |
| CommandArgumentError |                       | OperationalMode   |

### MakeAllUnknown

Raised in response to a command to make everyone in an area unknown

| **Result**          | **Reason**            | **Event Content** |
|---------------------|-----------------------|-------------------|
| Success             |                       |                   |
| FailedOnPermissions | NoPermisions          |                   |
|                     | NoRelevantPermissions |                   |
|                     | NoActivePermissions   |                   |

### MakeAllOut

Raised in response to a command to move everyone out of an area

| **Result**          | **Reason**            | **Event Content** |
|---------------------|-----------------------|-------------------|
| Success             |                       |                   |
| FailedOnPermissions | NoPermisions          |                   |
|                     | NoRelevantPermissions |                   |
|                     | NoActivePermissions   |                   |

### SetPersonState

Raised in response to a command to set a persons current state in an area

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       | PersonId          |
| FailedOnPermissions  | NoPermisions          | PersonId          |
|                      | NoRelevantPermissions | PersonId          |
|                      | NoActivePermissions   | PersonId          |
| CommandArgumentError |                       | [PersonId]        |

### Movement

| **Result**      | **Reason** | **Event Content**        |
|-----------------|------------|--------------------------|
| Success         |            | [PersonId],NewState      |
| ApbFailure      |            | PersonId                 |
| VerifyFailure   |            | PersonId                 |
| OccupancyLimit  |            | PersonId,OccupancyState  |
| ModeEnforcement |            | PersonId,OperationalMode |
| BarredEntry     |            | PersonId                 |

## Commands

For a command to be successful, it requires permission. There is a list of
security contexts for each command. These are linked to permissions. Providing
one comes back positive, the command is allowed.

### ChangeOperationalMode 

This command is used to request a change the operational mode or cancel an
existing command. All command requests are put on a list. The operational mode
is set to the command with the highest priority at any one time. If a command
times-out or is cancelled, it is taken off the list and the mode is revaluated.
With no commands on the list the mode goes to the default. To cancel a command
only the reference argument should be supplied.

Command Arguments:

-   Mode – Required mode

-   Period – Period that it lasts (optional)

-   Priority – 0 to 255, 0 being the highest

-   ReferenceId – Controlling Entity

-   PermissionOverrideId (optional)

### ChangePersonState

Changes a person’s state to in, out or unknown.

Command Arguments:

-   Person

-   NewState

### MakeAllUnknown

Makes all people in and out of area, unknown.

### MakeAllOutOfArea

Makes all people in area, out.

### ChangeEnforceOccupancyLimits 

Change whether the occupancy limits are enforced.

Command Arguments:

-   Enforced