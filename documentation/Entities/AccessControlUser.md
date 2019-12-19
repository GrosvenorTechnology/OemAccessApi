# AccessControl.User

The user entity is a unified view of all data related to a user, tokens, pins, permissions etc.

````json
{
    "entity": {
        "type": "AccessControl.User",
        "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
        "description": "Mr Stephen Woolhead (ABC123)",
        "enabledFrom": "2018-08-01T08:00:00",
        "enabledTo": "2019-01-01",
        "attributes": [
            "ImpairedMobility",
            "ImpairedHearing"
        ],
        "identifiers": [{
            "type": "AccessControl.Token",
            "id": "80213b75-eb3a-4376-8694-bf38d44b9772",
            "data": "1559635345",
            "tags": ["OfflineWL"]
        },
        {
            "type": "AccessControl.Token",
            "id": "80213b75-eb3a-4376-8694-bf38d44b9759",
            "data": "300009",
            "verifiers": [{
                "data": "1000:BnP/+uFM7XSwUVaX:e3AVUpvCiOhFLkHX",
                "duress": false
            },
            {
                "data": "1000:F3CDCaaYE1nALt7G:dO9SfcebMPO2TiCG",
                "duress": true
            }]
        }
        ],
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
        "permissions": {
            "Permission1": [{
                    "type": "systemMode",
                    "data": "Common.SystemMode:C858774D-6B01-493B-BF52-B1817A9E6AF3"
                },
                {
                    "type": "time",
                    "data": "Common.TimeTable:7DF8D397-73D1-4A63-9F10-0F646C4EA87F"
                }
            ],
            "Permission2": [
                { "type": "always" }
            ]
        }
    }
}
````

## Properties

### EnabledFrom

**[DateTime] (MinValue)** Defines the start date/time of the user. A user will not have access until this date/time.

### EnabledTo

**[DateTime] (MaxValue)** Defines the finishing date/time of the user. A user will not have access on or after this date/time.

### Attributes

**[enum[]] (empty)** A user can have zero or more attributes associated to them, these attributes are used to specify specific behaviours for the user. A common example of these is the *ImpairedSight* attribute that will cause a portal to use a sounder as well as the LED for indication.

- **ImpairedSight**
- **ImpairedHearing**
- **ImpairedMobility**

### Identifiers

````json
[
    {
        "type": "AccessControl.Token",
        "id": "80213b75-eb3a-4376-8694-bf38d44b9772",
        "data": "1559635345",
        "tags": ["OfflineWL"]
    },
    {
        "type": "AccessControl.Token",
        "id": "80213b75-eb3a-4376-8694-bf38d44b9759",
        "data": "300009",
        "verifiers": [{
            "data": "1000:BnP/+uFM7XSwUVaX:e3AVUpvCiOhFLkHX",
            "duress": false
        },
        {
            "data": "1000:F3CDCaaYE1nALt7G:dO9SfcebMPO2TiCG",
            "duress": true
        }]
    }
]
````

**[identifier[]] (empty)** A user can have zero or more identifiers associated to them. Identifiers uniquely identify a specific user. If the same identifier is used across multiple users the controller will raise a *DuplicateIdentifier* and the payload will include details of the clash. Identifiers default the type `AccessControl.Token` and this is the only supported type for now. Future identifiers types are planned such as biometrics.

The data property contains the data for the identifier. For a Token, this is the decoded string produced by the reader format. For a convention proximity card this might be a number or an ANPR might produce a car registration. For biometrics this will likely be a base64 encoded string.

Identifiers can have specific verifiers which will override the users general ones (see below).

It is possible to define an enabled period for an identifier by using the properties `EnabledFrom` and `EnabledTo` within the identifier definition. This works in much the same way as the same properties on the User.

### Verifiers

````json
[
    {
        "data": "1000:BnP/+uFM7XSwUVaX:e3AVUpvCiOhFLkHX",
        "duress": false
    },
    {
        "data": "1000:F3CDCaaYE1nALt7G:dO9SfcebMPO2TiCG",
        "duress": true
    }
]
````

**[verifier[]] (empty)** Verifiers provide secondary verification for a user that has been identified by an identifier. For example a PIN number. It is possible to also define a *duress* PIN that will also silently raise an event. Details on encoding the pin number are shown in the section on Encoding Pins.

### Permissions

````json
{
    "Portal-1_Entry":
    [
        {
            "type": "not",
            "data":
            {
                "type": "inlineTime",
                "data":
                {
                    "sets":
                    [
                    {
                        "days": ["Mo","Tu","We","Th","Fr"],
                        "periods": [
                        {
                            "start": "10:00:00",
                            "end": "12:00:00"
                        },
                        {
                            "start": "14:00:00",
                            "end": "16:00:00"
                        }
                        ]
                    }
                    ],
                    "holidayId": "BankHolidays"
                }
            }
        },
        {
            "type": "timePeriod",
            "data":
            {
                "start": "2018-08-01T00:00:00",
                "end": "2018-10-01"
            }
        }
    ]
}
````

*The example above is quite a complicated example to show the versatility permissions can have. In this case, the permission is active when `not` within the the specified `inlineTime` and is within the specified `timePeriod`. You will notice that the `inlineTime` is embedded in the `not`, reversing its normal effect.*

**[permission[]] (empty)** An array of named permissions. Each permission then has an array of security gates associated to it. All the gates have to be active (open) for a permission to be active.

N.B. If there are no gates, the permission is always active. See example below:

```json
{
    "Portal_Entry": [],
    "Portal_Exit": []
}
```

Please see the [Permission Gates](PermissionGates.md) document to see a full list of gates that can be used.

### Encoding Pins

Pins used in verifiers in the user record are hashed versions of the pin, the pins are strings and must match the target formatting required by the target reader type, no conversion will be don on the controller.  For numeric key pads, this means that the ping must be zero padded to match the number of expected pin digits, e.g. 0010 not 10.  

The following C# example will encode the pins for use on the controller.

```c#
using System;
using System.Security.Cryptography;

namespace GT.OemAccess.Utils
{
    public static class Hasher
    {
        // The following constants may be changed without breaking existing hashes.
        private const int SALT_BYTE_SIZE = 12;
        private const int HASH_BYTE_SIZE = 12;
        private const int PBKDF2_ITERATIONS = 1000;

        /// <summary>
        /// Creates a salted PBKDF2 hash of the password.
        /// </summary>
        /// <param name="pin">The pin to hash.</param>
        /// <returns>The hash of the password.</returns>
        public static string CreateHash(string pin)
        {
            // Generate a random salt
            var csprng = new RNGCryptoServiceProvider();
            var salt = new byte[SALT_BYTE_SIZE];
            csprng.GetBytes(salt);

            // Hash the password and encode the parameters
            var hash = PBKDF2(pin, salt, PBKDF2_ITERATIONS, HASH_BYTE_SIZE);
            return PBKDF2_ITERATIONS + ":" +
                Convert.ToBase64String(salt) + ":" +
                Convert.ToBase64String(hash);
        }

        /// <summary>
        /// Computes the PBKDF2-SHA1 hash of a string.
        /// </summary>
        /// <param name="pin">The pin to hash.</param>
        /// <param name="salt">The salt.</param>
        /// <param name="iterations">The PBKDF2 iteration count.</param>
        /// <param name="outputBytes">The length of the hash to generate, in bytes.</param>
        /// <returns>A hash of the password.</returns>
        private static byte[] PBKDF2(string pin, byte[] salt, int iterations, int outputBytes)
        {
            var pbkdf2 = new Rfc2898DeriveBytes(pin, salt) { IterationCount = iterations };
            return pbkdf2.GetBytes(outputBytes);
        }
    }
}
```
