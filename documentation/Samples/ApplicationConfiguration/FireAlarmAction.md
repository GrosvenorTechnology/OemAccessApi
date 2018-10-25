# Unlock portals on a fire alarm using a system mode

## Scenario

This example shows how you can get a fire alarm to unlock portals when active. We use a system mode, rather than directly commanding the portals, for simplicity and easy of maintenance of the config. Just a small change to each portal config is required to add/remove the portal to/from the fire alarm unlock.

## Implementation

The full JSON implementation can be seen [here](FireAlarmAction.json).

Below we explain how each element works.

### Set-up of a system mode

```JSON
        "systemModes": [
            {
                "id": "Fire-Alarm",
                "operationalMode": "off"
            }
        ]
```

Nothing of note here, just defining it.

### Set-up portals to control on the event of a fire alarm

```JSON
        "portals": [{
            "id": "FrontDoor-East",
            "address": "1-0-1",
            "lockType": "energiseToUnlock",
            "requestExitEnabled": true,
            "unlockOnSystemMode": {
                "id": "Fire-Alarm",
                "priority": 10
            },
            "entry": {
                "readers": ["RDR-FrontDoor-East"],
                "accessPermissions": ["GeneralAccess"]
            }
        },
        {
            "id": "FrontDoor-West",
            "address": "1-0-2",
            "lockType": "energiseToUnlock",
            "requestExitEnabled": true,
            "unlockOnSystemMode": {
                "id": "Fire-Alarm",
                "priority": 10
            },
            "entry": {
                "readers": ["RDR-FrontDoor-West"],
                "accessPermissions": ["GeneralAccess"]
            }
        }]
```

The only thing to note here is the `unlockOnSystemMode` property is set to the "Fire-Alarm" System Mode.

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

### Set-up of fire alarm input

```JSON
        "inputs": [{
                "id": "FireAlarm",
                "address": "1-0-1",
                "normallyOpen": false
            }
        ]
```

Set-up of the input is simple. The output from the fire alarm is normally a pair of "clean" closed contacts, which open on an alarm.

### Set-up of the action

```JSON
        "actions":[{
                "id": "UnlockOnFireAlarm",
                "sourceType": "Hardware.Input",
                "sourceIds": ["FireAlarm"],
                "targetType": "Common.SystemMode",
                "targetIds": ["Fire-Alarm"],
                "map":
                [
                    {
                        "event": "InputState:active",
                        "command": "ChangeMode",
                        "arguments":
                        {
                            "mode": "On"
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

This action monitors the fire alarm input for it to go 'active'. When this happens the system mode "Fire-Alarm" is turned on (and the portals are unlocked), by issuing a change mode command to the system mode. On going 'inactive', the change mode command is cleared.

The action has also a `restoreCommand` "ClearOwnedFromModeStack". This command will be issued on the action being disabled, changed or deleted. In the event of one of these things happening, any outstanding change mode commands will be cleared. In this case, the system mode will be reset if turned on by this action.
