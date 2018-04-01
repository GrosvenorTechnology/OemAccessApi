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
            "data": "1559635345"
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
                        "data": "AccessControl.TimeTable:7DF8D397-73D1-4A63-9F10-0F646C4EA87F"
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