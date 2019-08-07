# Common.SystemMode

**Supported >= 3.2.0**

System Modes can be used by permission gates and actions. They are controlled by changing their operational mode, either directly or indirectly by using actions.

A system mode has the following operational modes:

- **off** (default) - The active state will be false.
- **on** - The active state will be true.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

````json
{
    "controller": {
        "systemModes": [{
                "id": "emergency",
                "operationalMode": "off",
                "changeModePermissions": ["security"]
            }
        ]
    }
}
````

## Properties

### operationalMode

**[enum] (off)** Specifies which operational mode is the default.

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the `ChangeMode` command.

## States

### OperationalMode

**[enum]** This shows the current operational mode for the system mode.

- **off** (default) - The active state will be off.
- **on** - The active state will be on.

### Active

**[bool]** The state of the system mode.

## Events

No Standalone events.

## Commands

No Standalone commands.
