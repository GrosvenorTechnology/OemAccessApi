# Using *First Person Unlock* on a Portal

## Scenario

This example shows how you can get a portal to unlock during the time-table's periods, but only after the first valid read of each time period.

The reason you may want to do this, is when you want to ensure that no one can "wonder in" until an authorised person has arrived for that period.

## Implementation

The full JSON implementation can be seen [here](FirstPersonUnlock.json).

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
            }
        ],
        "portals": [
            {
                "id": "Door-101",
                "description": "Door 101",
                "address": "1-0-1",
                "operationalMode": "normal",
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

Nothing of note here, just defining a reader, portal and time table. The time table defines the periods the portal will be unlocked (after the first valid read of the period).

### Set-up of the action

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
                            "Mode": "FirstPersonUnlock"
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
            }
        ]
```

This action takes the time table state `TimeTableState` as its input and changes the target's portal mode to `FirstPersonUnlock` when the state changes to `InPeriod`. The `OutOfPeriod` then resets the portal mode (which will lock the portal, assuming a valid token unlocked the portal during that period).

The action has also a `restoreCommand` "ClearOwnedFromModeStack". This command will be issued on the action being disabled, changed or deleted. In the event of one of these things happening, any outstanding change mode commands will be cleared. In this case, the portal will be locked.
