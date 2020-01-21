# Using *First Person Unlock* on a Portal, reset by second reader

## Scenario

This example shows how you can get a portal to unlock during the time-table's periods, but only after the first valid read of each time period. A second reader is used to re-lock the portal before the time-table period expires.

The reason you may want to do this, is when you want to ensure that no one can "wonder in" until an authorised person has arrived for that period. If the authorised person leaves before the end of the period, the 'reset reader' can be used to lock the portal, ready for the next person.

## Implementation

The full JSON implementation can be seen [here](FirstPersonUnlockResetBySecondReader.json).

Below we explain how each element works. It assumes the default portal mode is `Normal`.

### Set-up of the reader, portal and time table

```json

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
                "description": "Reader to reset door 101",
                "address": "1-0-2",
                "operationalMode": "tokenOnly",
                "tokenFormatType": "RDSATEONPRO"
            }
        ],
        "portals": [
            {
                "id": "Door-101",
                "description": "Door 101",
                "address": "1-0-1",
                "operationalMode": "normal",
                "changeModePermissions": [
                    "PortalUnlock"
                ],
                "entry": {
                    "readers": [
                        "Reader-101"
                    ],
                    "accessPermissions": [
                        "PortalUnlock"
                    ]
                }
            }
        ],
        "timeTables": [
            {
                "type": "Common.TimeTable",
                "id": "DoorUnlocked",
                "description": "Example Time Table",
                "sets": [
                    {
                        "days": [
                            "Mo",
                            "Tu",
                            "We",
                            "Th",
                            "Fr"
                        ],
                        "periods": [
                            {
                                "start": "09:00:00",
                                "end": "17:00:00"
                            }
                        ]
                    }
                ],
                "holidayId": "BankHolidays"
            }
        ]
```

Here we define the two readers, the portal and time table. The time table defines the periods the portal will be unlocked (after the first valid read of the period). Note the `changeModePermissions` on the portal, which will be used by the reset reader action.

### Set-up of the actions

```json
        "actions": [
            {
                "id": "UnlockOnFirstRead",
                "sourceType": "Common.TimeTable",
                "sourceIds": [
                    "DoorUnlocked"
                ],
                "targetType": "AccessControl.Portal",
                "targetIds": [
                    "Door-101"
                ],
                "map": [
                    {
                        "event": "TimeTableState:InPeriod",
                        "command": "ChangeMode",
                        "arguments": {
                            "Mode": "FirstPersonUnlock",
                            "Priority": 10
                        }
                    },
                    {
                        "event": "TimeTableState:OutOfPeriod",
                        "command": "ChangeMode"
                    }
                ],
                "restoreCommand": {
                    "command": "ClearOwnedFromModeStack"
                }
            },
            {
                "id": "Relock",
                "sourceType": "Hardware.Reader",
                "sourceIds": [
                    "Reader-102"
                ],
                "targetType": "AccessControl.Portal",
                "targetIds": [
                    "Door-101"
                ],
                "permissionsFrom": "person",
                "map": [
                    {
                        "event": "Read:Success",
                        "command": "ChangeMode",
                        "arguments": {
                            "Mode": "Normal",
                            "Period": "00:00:02",
                            "Priority": 5
                        }
                    }
                ]
            }
        ]
```

The first action takes the time table state `TimeTableState` as its input and changes the target's portal mode to `FirstPersonUnlock` when the state changes to `InPeriod`. The `OutOfPeriod` then resets the portal mode (which will lock the portal, assuming a valid token unlocked the portal during that period). The action has also a `restoreCommand` "ClearOwnedFromModeStack". This command will be issued on the action being disabled, changed or deleted. In the event of one of these things happening, any outstanding change mode commands will be cleared. In this case, the portal will be locked.

The second action takes a read from the 'reset reader' and changes the mode of the portal to `normal` for 2 seconds. This second action has to have a higher priority than the first action, to override the `FirstPersonUnlock` mode that the first action may have set. Note the `permissionFrom` property of this action is set to `person`. This allows only people who can unlock the portal, to be able to reset it.
