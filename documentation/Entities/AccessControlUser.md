# AccessControl.User

````json
{
    "entity": {
        "type": "AccessControl.User",
        "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
        "description": "Mr Stephen Woolhead (ABC123)",
        "attributes": [
            "VIP",
            "Veritgo"
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

A list of permissions with the security gates associated to each permission.