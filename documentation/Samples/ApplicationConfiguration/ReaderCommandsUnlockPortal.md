# Using reader commands to unlock portals

## Scenario

This is an example of using a reader with a keypad to unlock and relock portals. A person with permission can unlock the portals by pressing a number and # on the keypad followed by the presentation of their card (and optionally a PIN). It is the same process to relock the portal using a different number. The reader can also be used on a portal in the normal way.

## Implementation

The full JSON implementation can be seen [here](ReaderCommandsUnlockPortal.json).

Below we explain how each element works.

### Set-up of the portals

```JSON
        "portals": [
        {
            "id": "FrontDoor-East",
            "address": "1-0-1",
            "requestExitEnabled": true,
            "entry": {
                "readers": ["RDR-FrontDoor-East"],
                "accessPermissions": ["GeneralAccess"]
            },
            "changeModePermissions": ["FrontDoorUnlock"]
        },
        {
            "id": "FrontDoor-West",
            "address": "1-0-2",
            "requestExitEnabled": true,
            "entry": {
                "readers": ["RDR-FrontDoor-West"],
                "accessPermissions": ["GeneralAccess"]
            },
            "changeModePermissions": ["FrontDoorUnlock"]
        }]
```

The only thing to note here is the `changeModePermissions` property on the portals has a permission called "FrontDoorUnlock". This permission would need to be given to people allowed to unlock/relock the portals.

### Set-up of the readers

```JSON
        "readers": [
            {
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

Nothing of note here.

### Set-up of action

```JSON
        "actions":[
           {
                "id": "UserUnlock",
                "sourceType": "Hardware.Reader",
                "sourceIds": ["RDR-FrontDoor-East", "RDR-FrontDoor-West"],
                "targetType": "AccessControl.Portal",
                "targetIds": ["FrontDoor-East", "FrontDoor-West"],
                "map":
                [
                    {
                        "event": "Read:1:success",
                        "command": "ChangeMode",
                        "arguments":
                        {
                            "mode": "Unlocked"
                        }
                    },
                    {
                        "event": "Read:2:success",
                        "command": "ChangeMode"
                    }
                ],
                "restoreCommand":
                {
                    "command": "ClearOwnedFromModeStack"
                },
                "permissionsFrom": "person",
                "referenceFrom": "action"
            }
        ]
```

This action will monitor both readers for reads using the command numbers 1 and 2. Command number 1 unlocks both portals using the `ChangeMode` command, command number 2 clears the mode using the 'empty' `ChangeMode` command and so relock the portals. Because the `permissionsFrom` property is set to "person", the `ChangeMode` command will only be successful if the person has permission (see the portal set-up). The `referenceFrom` property determines the behaviour and in this case is set to "action". This means any person (with permission) at either reader can unlock the portals and some other person at either reader can restore (relock) the portals. This is because the reference is always the same. The action has also a `restoreCommand` "ClearOwnedFromModeStack". This command will be issued on the action being disabled, changed or deleted. In the event of one of these things happening, any outstanding change mode commands will be cleared. In this case the portals will be relocked.
