# Unlock portals on a fire alarm, with two separate fire alarm inputs

## Scenario

This example shows how you can get multiple fire alarm inputs to unlock the same portals when at least one of them is active.

## Implementation

The full JSON implementation can be seen [here](FireAlarmActionAdvanced.json).

Below we explain how each element works.

### Set-up portals to control on the event of a fire alarm

```JSON
        "portals": [{
            "id": "FrontDoor-East",
            "address": "1-0-1",
            "requestExitEnabled": true,
            "entry": {
                "readers": ["RDR-FrontDoor-East"],
                "accessPermissions": ["GeneralAccess"]
            }
        },
        {
            "id": "FrontDoor-West",
            "address": "1-0-2",
            "requestExitEnabled": true,
            "entry": {
                "readers": ["RDR-FrontDoor-West"],
                "accessPermissions": ["GeneralAccess"]
            }
        }]
```

Nothing to note here, just defining them.

### Set-up of readers

```JSON
        "readers": [{
                "id": "RDR-FrontDoor-East",
                "address": "1-0-1",
                "tokenFormatType": "RDSATEONPRO"
            },
            {
                "id": "RDR-FrontDoor-West",
                "address": "1-0-2",
                "tokenFormatType": "RDSATEONPRO"
            }
        ]
```

Nothing to note here, just defining them.

### Set-up of fire alarm inputs

```JSON
        "inputs": [{
                "id": "FireAlarm-1",
                "address": "1-0-1",
                "normallyOpen": false
            },
            {
                "id": "FireAlarm-2",
                "address": "1-0-2",
                "normallyOpen": false
            }
        ]
```

Set-up of the inputs is simple. The output from a fire alarm is normally a pair of "clean" closed contacts, which open on an alarm.

### Set-up of the action

```JSON
        "actions":
        [
            {
                "id": "UnlockOnFireAlarm",
                "sourceType": "Hardware.Input",
                "sourceIds": ["FireAlarm-1", "FireAlarm-2"],
                "targetType": "AccessControl.Portal",
                "targetIds": ["FrontDoor-East", "FrontDoor-West"],
                "referenceFrom": "source",
                "map":
                [
                    {
                        "event": "InputState:active",
                        "command": "ChangeMode",
                        "arguments":
                        {
                            "Mode": "Unlocked",
                            "priority": 10
                        }
                    },
                    {
                        "event": "InputState:inactive",
                        "command": "ChangeMode"
                    }
                ],
                "restoreCommand":
                {
                    "command": "ClearOwnedFromModeStack"
                }
            }
        ]
```

This action monitors the fire alarm inputs for one to go 'active'. When this happens the portals are unlocked, by issuing a change mode command to the portals. On going 'inactive', the change mode command is cleared.

The important thing here is the `referenceFrom` property is set to 'source'. Without this, the action would be used as the reference, and the inputs would 'overwrite' each other i.e. both fire alarms go off, one then resets, which locks the portals - not what you want! Setting the `referenceFrom` property to 'source' makes the portals store individual change mode commands, one for each input. So the portals are only relocked when both fire alarms reset.

The action has also a `restoreCommand` "ClearOwnedFromModeStack". This command will be issued on the action being disabled, changed or deleted. In the event of one of these things happening, any outstanding change mode commands will be cleared.
