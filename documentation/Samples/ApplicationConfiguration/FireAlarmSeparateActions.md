# Unlock portals on a fire alarm using separate Actions

## Scenario

This example shows how you can get a fire alarm to unlock portals when activated and the portals are kept unlocked until a person with permission presents their card to a designated reader. The portals are prevented from being relocked while the fire alarm is still active.

## Implementation

The full JSON implementation can be seen [here](FireAlarmSeparateActions.json).

Below we explain how each element works.

### Set-up a portal to control on the event of a fire alarm

```JSON
        "portals": [
            {
                "id": "Door-101",
                "description": "Door 101",
                "address": "1-0-1",
                "operationalMode": "normal",
                "changeModePermissions":["ResetFireAlarmPermission"],
                "entry": {
                    "readers": [
                        "Reader-101"
                    ],
                    "accessPermissions": [
                        "Door-101"
                    ]
                }
            }
        ]
```

The only thing to note here is the "ResetFireAlarmPermission" in the `changeModePermissions` property. Only people with this permission will be able to relock the portals after a fire alarm.

### Set-up of readers

```JSON
        "readers": [
            {
                "id": "Reader-101",
                "description": "Reader on door 101",
                "address": "1-0-1",
                "operationalMode": "tokenOnly",
                "tokenFormatType": "RDSATEONPRO"
            },
            {
                "id": "Reader-102",
                "description": "Reader to relock doors after fire alarm",
                "address": "1-0-2",
                "operationalMode": "tokenOnly",
                "tokenFormatType": "RDSATEONPRO"
            }
        ]
```

Here we have the definition of the reader on the portal and the reader to allow the relock of the portals after a fire alarm. Note that "Reader-102" does not need to be connected to a portal.

### Set-up of fire alarm input

```JSON
        "inputs": [
            {
                "id": "Input-101",
                "description": "Fire Alarm Input",
                "operationalMode": "enabled",
                "address": "1-0-1",
                "normallyOpen": false,
                "inputType": "unsupervised",
                "InputOperationType": "normal"
            }
        ]
```

Set-up of the input is simple. The output of the fire alarm is normally closed contacts opening on an alarm.

### Set-up of the actions

```JSON
        "actions": [{
            "id": "UnlockOnFireAlarm",
            "sourceType": "Hardware.Input",
            "sourceIds": ["Input-101"],
            "targetType": "AccessControl.Portal",
            "targetIds": ["Door-101"],
            "map": [
                {
                    "event": "InputState:active",
                    "command": "ChangeMode",
                    "arguments":
                    {
                        "Mode": "Unlocked",
                        "Reference": "FireAlarmCommand"
                    }
                }
            ],
            "restoreCommand": {
                "command": "ClearOwnedFromModeStack"
              }
        },
        {
            "id": "ResetFireAlarm",
            "sourceType": "Hardware.Reader",
            "sourceIds": ["Reader-102"],
            "targetType": "AccessControl.Portal",
            "targetIds": ["Door-101"],
            "permissionsFrom": "person",
            "map": [
                {
                    "event": "Read:Success",
                    "command": "ChangeMode",
                    "arguments":
                    {
                        "Reference": "FireAlarmCommand"
                    }
                }
            ]
        },
        {
            "id": "DisableFireAlarmReset",
            "sourceType": "Hardware.Input",
            "sourceIds": ["Input-101"],
            "targetType": "Common.Action",
            "targetIds": ["ResetFireAlarm"],
            "map": [
                {
                    "event": "InputState:active",
                    "command": "ChangeMode",
                    "arguments":
                    {
                        "Mode": "Disabled"
                    }
                },
                {
                    "event": "InputState:inactive",
                    "command": "ChangeMode"
                }
            ],
            "restoreCommand": {
                "command": "ClearOwnedFromModeStack"
            }
        }
    ]}
```

We have three actions set-up as follows:

#### Action "UnlockOnFireAlarm"

This action monitors the fire alarm input for it to go active. When this happens the portal is unlocked by issuing a change mode command to the portal. One of the arguments of the command is a reference "FireAlarmCommand". This will be used in the next action to clear the change mode command. The action has also a `restoreCommand` "ClearOwnedFromModeStack". This command will be issued on the action being disabled, changed or deleted. In the event of one of these things happening, any outstanding change mode commands will be cleared. In this case, the portal will be reset if unlocked by this action.

#### Action "ResetFireAlarm"

This action monitors the reader that is to be used to reset the unlocked portals. On seeing a read from the reader, a change mode reset command will be sent to the portal using the same reference as the first action. The `permissionsFrom` property on the action is set to "person". This will ensure only people with the "ResetFireAlarmPermission" permission can reset the portals (see portal set-up).

#### Action "DisableFireAlarmReset"

This action is used to prevent the resetting of the portal while the fire alarm is still active. This is achieved by disabling the "ResetFireAlarm" action while the fire alarm input is active. The action has also a `restoreCommand` "ClearOwnedFromModeStack". This command will be issued on the action being disabled, changed or deleted. In the event of one of these things happening, any outstanding change mode commands will be cleared. In this case the "ResetFireAlarm" action will be re-enabled.

## Conclusion

This is a good way of controlling a couple of portals, but the `targetIds` of the first two actions need to be updated when adding or removing portals to be controlled. A better way is to use a System Mode, which can also allow you to change access permissions on the event of a fire. See sample [Unlock portals on a fire alarm using separate Actions and a System Mode](FireAlarmSeparateActionsUsingSystemMode.md) to do this.