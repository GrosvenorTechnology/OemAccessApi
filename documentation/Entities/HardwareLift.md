# Hardware.Lift

**Supported >= 4.1.0**

A lift provides a simple way to limit access to floors in a lift.  When a user swipes a card on the lift reader(s), they will be granted access to zero or more floors based on their access.

Access to the floors is enabled by operating outputs, that can either be linked to the lift control system, or physically inline the the floor push button switches.

The Lift has the following operational modes:

- **normal** (Default) – The the lift is enabled and will allow access.
- **disabled** - The lift will not grant access to a floor.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

````json
{
    "controller": {
        "lifts": [{
            "id": "lift-1",
            "description": "Reception Lift",
            "operationalMode": "normal",
            "operateTime": "00:00:05",
            "readers": ["lift-1-reader"],
            "floors":[
                {
                    "id": "lift-1-floor-1",
                    "address": "2-0-1",
                    "operationalMode": "Normal",
                    "accessPermissions": ["Floor1-Access"]
                },
                {
                    "id": "lift-1-floor-2",
                    "address": "2-0-2",
                    "operationalMode": "Normal",
                    "accessPermissions": ["Floor2-Access"]
                }
            ]
        }]
    }
}
````

## Properties

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### readers

**[entityId[]] (empty)** A list of reader Ids that the lift should accept
identities from.

### operateTime

**[timespan] (00:00:10)** The time the floor switches are enabled for.

### floors

**[Hardware.LiftFloor[]] (empty)** A list of floor definitions. See [Hardware.LiftFloor](HardwareLiftFloor.md) for how they are defined.

### commandPermissions

**[string[]] (empty)** A person with a permission on this list can execute any command on this lift other than `ChangeMode` command which has it's own permissions.

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the `ChangeMode` command.

## States

### OperationalMode

**[enum]** This shows the current operational mode for the output.

## Events

### LiftUsed

- **PersonId [identifier]** – The identifier for the person who used the lift.

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**   |
|------------------|---------------------|
| Success              | PersonId |
| FailedOnPermissions  | PersonId |

## Commands

### RequestUse [internal]

- **PersonId [identifier]** – The unique identifier for the Person requesting to use the lift

| **Result**          | **Reason**            | **Event Content** |
|---------------------|-----------------------|-------------------|
| Success             |                       | PersonId          |
| FailedOnPermissions | NoPermissions         | PersonId          |
|                     | NoRelevantPermissions | PersonId          |
|                     | NoActivePermissions   | PersonId          |
|                     | LiftDisabled          | PersonId          |
