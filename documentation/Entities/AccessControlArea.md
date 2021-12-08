# AccessControl.Area

An Area is the logical representation of a controlled area. Portals are the
normal entry and exit points of an area, though commands can also be used. The area will keep a list of people known to be in or out of the area. People not known to the area will be set to their relevant state as soon as the area deals with them.

Areas can work over more than one controller. To allow for this a multi-master concept has been adopted. When a change happens, the originating area instance is responsible for creating the event and sending state, other area instances then set their state without repeating the event or state message. This should keep the instances in-sync. There will normally be an area instance in a host service. An instance can ask for a re-sync, which would normally be provided by the host service.

An area can be commanded to be in a operational mode. The default mode is set in the area configuration.

Whatever the mode, the area will keep account of who is in or out of the area. Restrictions on occupancy limits will also always apply when in ‘Enforce Occupancy Limits’ state, though certain people can be made exempt.

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

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

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
            "minimumOccupancy": 0,
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

**[enum] (enforced)** Specifies which operational mode is the default.

### offlineMode

**[enum] (unenforced)** Specifies which operational mode is used when the controller is off-line.

### enforceOccupancyLimits

**[Boolean] (true)** If set to true occupancy limits will be enforced.

### maximumOccupancy

**[int] (1000000)** The maximum number of people allowed in the area, when
enforceOccupancyLimits is set to true.

### minimumOccupancy

**[int] (0)** The minimum number of people allowed in the area, when
enforceOccupancyLimits is set to true.

### changeModePermissions

**[string[]] (empty)** The list of permissions that allow the area mode to be changed.

### setPersonStatePermission

**[string[]] (empty)** The list of permissions that allow the state of an individual
user to be changed.

### makeAllUnknownPermission

**[string[]] (empty)** The list of permissions that allow all users in an area to be
made unknown.

### makeAllOutPermissions

**[string[]] (empty)** The list of permissions that allow all users in an area to be
marked as out of the area.

## States

### OperationalMode

**[enum]** This shows the current area mode (see Area Operational Modes for
details).

### Occupancy

**[int]** Shows the number of people in the area..

### OccupancyState

**[enum]** Shows one of the following:

- belowMinimum
- atMinimum
- oK
- atMaximum
- aboveMaximum

## Events

### Movement

Report of all movements

| **Result**      | **Event Content**        |
|-----------------|--------------------------|
| Success         | [PersonId],NewState      |
| ApbFailure      | PersonId                 |
| VerifyFailure   | PersonId                 |
| OccupancyLimit  | PersonId,OccupancyState  |
| ModeEnforcement | PersonId,OperationalMode |
| BarredEntry     | PersonId                 |

## Commands

### MakeAllUnknown

Make everyone in an area unknown

| **Result**          | **Reason**            | **Event Content** |
|---------------------|-----------------------|-------------------|
| Success             |                       |                   |
| FailedOnPermissions | NoPermissions          |                   |
|                     | NoRelevantPermissions |                   |
|                     | NoActivePermissions   |                   |

### MakeAllOut

Move everyone out of an area

| **Result**          | **Reason**            | **Event Content** |
|---------------------|-----------------------|-------------------|
| Success             |                       |                   |
| FailedOnPermissions | NoPermissions          |                   |
|                     | NoRelevantPermissions |                   |
|                     | NoActivePermissions   |                   |

### SetPersonState

Set a persons current state in an area

Parameters:

- **PersonId [identifier]** – The unique identifier for the Person
- **State [enum] (optional)** - The default is `Unknown`. Options: `Out`, `In` , `Unknown`

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       | PersonId          |
| FailedOnPermissions  | NoPermissions          | PersonId          |
|                      | NoRelevantPermissions | PersonId          |
|                      | NoActivePermissions   | PersonId          |
| CommandArgumentError |                       | [PersonId]        |
