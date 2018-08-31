# AccessControl.User

The user entity is a unified view of all data related to a user, tokens, pins, permissions etc.

````json
{
    "entity": {
        "type": "AccessControl.User",
        "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
        "description": "Mr Stephen Woolhead (ABC123)",
        "attributes": [
            "ImpairedMobility",
            "ImpairedHearing"
        ],
        "identifiers": [{
            "type": "AccessControl.Token",
            "id": "80213b75-eb3a-4376-8694-bf38d44b9772",
            "data": "1559635345",
            "tags": ["OfflineWL"]
        }],
        "verifiers": [{
                "type": "AccessControl.Pin",
                "id": "465b1981-6805-4737-906e-786a423c43cd",
                "data": "MTIzNGZkc2Y="
            },
            {
                "type": "AccessControl.Pin",
                "id": "227f78a5-4a6a-42b5-97b0-e4cf8d844b92",
                "duress": true,
                "data": "c3RlcGhlbg=="
            }
        ],
        "permissions": [{
                "Permission1": [{
                        "type": "mode",
                        "data": "AccessControl.Mode:C858774D-6B01-493B-BF52-B1817A9E6AF3"
                    },
                    {
                        "type": "time",
                        "data": "Common.TimeTable:7DF8D397-73D1-4A63-9F10-0F646C4EA87F"
                    }
                ]
            },
            {
                "Permission2": [
                    { "type": "always" }
                ]
            }
        ]
    }
}
````

## Properties

### Attributes

- **ImpairedSight**
- **ImpairedHearing**
- **ImpairedMobility**

A user can have zero or more attributes associated to them, these attributes are
used to specify specific behaviours for the user. A common example of these is
the *ImpairedSight* attribute that will cause a portal to use a sounder rather than the
LED for indication.

### Identifiers

A user can have zero or more identifiers associated to them. Identifiers
uniquely identify a specific user. If the same identifier is used across
multiple users the controller will raise a *DuplicateIdentifier* and the payload
will include details of the clash. Identifiers default the type
AccessControl.Token and this is the only supported type for now. Future
identifiers types are planned such as biometrics.

The data property contains the data for the identifier. For a Token, this is the
decoded string produced by the reader format. For a convention proximity card
this might be a number or an ANPR might produce a car registration. For
biometrics this will likely be a base64 encoded string.

### Verifiers

Verifiers provide secondary verification for a user that has been identified by
an identifier. For example a PIN number.

### Permissions

An array of named permissions. Each permission has an array with the security gates associated
to it.  The results of all individual gates will be AND'd together to produce the final result.

The following types of gates are currently supported:

#### Always

```json
{
    "type": "always"
}
```

The always gate always returns true.

### Mode

```json
{
    "type": "mode",
    "data": "NormalWorking"
}
```

Checks if the system mode referenced in the data section is currently active.  The data section is
expected to be an EntityKey in the format Namespace.Type:Id.  If only the Id is provided then
System.Mode will be assumed as the default for the entity type.

#### Not

```json
{
    "type": "not",
    "data": [{
        "type": "time",
        "data": "Mon-Fri-9-17"
    }]
}
```

The not gate will invert the final response of an array of gates. This example will return true outside
the time range specified.

#### Time

```json
{
    "type": "time",
    "data": "Mon-Fri-9-17"
}
```

Checks if the time table referenced in the data section is currently active.  The data section is 
expected to be an EntityKey in the format Namespace.Type:Id.  If only the Id is provided then 
Common.TimeTable will be assumed as the default for the entity type.

#### InlineTime [>= 3.0.0]

```json
{
    "type": "inlineTime",
    "data": {
            "monday": [
                {
                    "start": "9:00:00",
                    "end": "17:00:00"
                }
            ],
            "tuesday": [
                {
                    "start": "9:00:00",
                    "end": "12:00:00"
                },
                {
                    "start": "14:00:00",
                    "end": "18:00:00"
                }
            ]
        }
}
```

Similar to the `time` gate, this gate will return true if the time falls inside one of the transitions specified.
The format of the data section is the same as the `transitions` section of the [Common.TimeTable](CommonTimeTable.md) type.