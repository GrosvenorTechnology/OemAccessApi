# Hardware.LiftFloor

**Supported >= 4.1.0**

This entity is only defined within the Hardware.Lift entity.  See [Hardware.Lift](HardwareLift.md) for where they are defined.

The lift floor entity is used to operate an output to allow a lift user to select that particular floor. The output would normally be used to enable the floor push button in the lift.

The lift floor has the following operational modes:

- **normal** (Default) – The output will change in response to the `RequestFloor` command, providing the user has an access permission.
- **noAccess** - The output will stay off (disabling the push button). The output will only respond to ChangeMode commands.
- **openAccess** - The output will stay on (enabling the push button). The output will only respond to ChangeMode commands.

Changing the mode with an action can allow general access to a floor, or completely prevent anyone accessing a floor altogether.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

````json
{
    "id": "lift-1-floor-1",
    "address": "2-0-1",
    "operationalMode": "Normal",
    "accessPermissions": ["Floor1-Access"]
}
````

## Properties

### address

**[hardware-address]** The controller logical address for the output.

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### accessPermissions

**[string[]] (empty)** A person with a permission on this list can execute the internal `RequestFloor` command. The `ChangeMode` command has it's own permissions.

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the `ChangeMode` command.

## States

### OperationalMode

**[enum]** This shows the current operational mode for the output.

### ConnectedToBlade

**[boolean]** **[diagnostic]** Is the output available on a connected blade.

## Events

No Standalone events.

## Commands

### RequestFloor (internal)

This command will turn the output on for a given period. This will enable the use of the push button associated with the floor.

- **Period [timespan]** – The time the output is operated for.

| **Result**           | **Reason**            | **Event Content** |
|----------------------|-----------------------|-------------------|
| Success              |                       |                   |
| FailedOnPermissions  | NoPermissions         |                   |
|                      | NoActivePermissions   |                   |
|                      | NoRelevantPermissions |                   |
| FailedBecauseOfError |                       |                   |
| CommandArgumentError |                       |                   |
