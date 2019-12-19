# Common.SharedPermissionSet

**Supported >= 4.3.0**.

Shared Permission Sets can be used by the `sharedPermissionSet` permission gate (defined in a `User` record under the `permissions` property).

Shared Permission Sets can be defined in the Application Configuration and in Entity Change messages:

- In the Application Configuration

````json
{
    "controller": {
        "sharedPermissionSets": [
            {
                "type": "Common.SharedPermissionSet",
                "id": "SPS1",
                "description": "Example Shared Permission Set",
                "gates": [
                    {
                        "type": "inlineTime",
                        "data": [
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
                                        "start": "09:45:00",
                                        "end": "13:00:00"
                                    },
                                    {
                                        "start": "14:00:00",
                                        "end": "17:30:00"
                                    }
                                ]
                            }
                        ]
                    }
                ]
            }
        ]
    }
}
````

- In an Entity Change

````json
{
    "entity": {
        "type": "Common.SharedPermissionSet",
        "id": "SPS2",
        "description": "Example of Shared Permission Set",
        "gates": [
            {
                "type": "inlineTime",
                "data": [
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
                                "end": "13:00:00"
                            },
                            {
                                "start": "14:00:00",
                                "end": "17:30:00"
                            }
                        ]
                    }
                ]
            }
        ]
    }
}
````

## Properties

### gates

**[gate[]] (empty)** A list of one or more gates. If there is more than one root gate, they are AND together (all need to be true).

Please see the [Permission Gates](PermissionGates.md) document to see a full list of gates that can be used.
