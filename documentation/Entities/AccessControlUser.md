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

The following types of gates are currently supported:

#### Always

```json
{
    "type": "always"
}
```

The `always` gate is always active. Not strictly required.

#### SystemMode

> was `mode` - will still work, but now depreciated to avoid confusion with operational modes.

```json
{
    "type": "systemMode",
    "data": "Emergency"
}
```

Checks if the system mode referenced in the data section is currently active.  The data section is expected to be an EntityKey in the format Namespace.Type:Id.  If only the Id is provided then Common.SystemMode will be assumed as the default for the entity type.

#### Not

```json
{
    "type": "not",
    "data": {
        "type": "time",
        "data": "Mon-Fri-9-17"
    }
}
```

The `NOT` gate will invert the response of the child gate. This example will be active outside the time range specified.

#### And

```json
{
    "type": "and",
    "data": [
    {
        "type": "time",
        "data": "Mon-Fri-9-17"
    },
    {
        "type": "systemMode",
        "data": "NormalWorking"
    }
    ]
}
```

For the `AND` gate to be active, all the child gates need to be active.

#### Or

```json
{
    "type": "or",
    "data": [
    {
        "type": "time",
        "data": "Mon-Fri-9-17"
    },
    {
        "type": "systemMode",
        "data": "Emergency"
    }
    ]
}
```

For the `OR` gate to be active, at least one of the child gates needs to be active.

#### TimePeriod

```json
{
    "type": "timePeriod",
    "data":
    {
        "start": "2018-08-01T00:00:00",
        "end": "2018-10-01"
    }
}
```

Checks to see if the current date-time is within the specified start and end date-times, if so the gate is active. If you do not specify a time, it defaults to 00:00:00 of the day specified.

#### Time

```json
{
    "type": "time",
    "data": "Mon-Fri-9-17"
}
```

Checks if the time table referenced in the data section is currently active.  The data section is expected to be an EntityKey in the format Namespace.Type:Id.  If only the Id is provided then Common.TimeTable will be assumed as the default for the entity type.

#### Holiday

```json
{
    "type": "holiday",
    "data": "BankHolidays"
}
```

Checks if the holiday table referenced in the data section is currently active.  The data section is expected to be an EntityKey in the format Namespace.Type:Id.  If only the Id is provided then Common.Holiday will be assumed as the default for the entity type.

#### InlineTime

There are two ways of defining an inline time:

##### Method 1

```json
{
    "type": "inlineTime",
    "data": {
        "transitions": {
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
        },
        "holidayId": "BankHolidays"
    }
}
```

The days of the week enumeration is: [Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Holiday].

The `holidayId` property is optional. When used, it points to a record in the holiday table. When in a holiday period, rather than using the time periods for the relevant day, it uses the `Holiday` time periods. If there are no time periods defined for the holiday period, the person will be denied access. This is useful for Bank Holidays.

Similar to the `time` gate, this gate will be active if the time falls inside one of the transitions specified. The format is the same as the `transitions` method of the [Common.TimeTable](CommonTimeTable.md) type.

##### Method 2

```json
{
    "type": "inlineTime",
    "data": {
        "sets": [
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
```

This method tends to be more compact when defining regular periods throughout the week.

The `days` enumeration is: [Su, Mo, Tu, We, Th, Fr, Sa, Ho].

The `holidayId` property is optional. When used, it points to a record in the holiday table. When in a holiday period, rather than using the time periods for the relevant day, it uses the `Ho` (holiday) time periods. If there are no time periods defined for the holiday period, the person will be denied access. This is useful for Bank Holidays.

Similar to the `time` gate, this gate will be active if the time falls inside one of the specified periods. The format is the same as the `sets` method of the [Common.TimeTable](CommonTimeTable.md) type.

>N.B. There were two older methods which still can be used, but have now been deprecated. They are shown below for reference:

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

```json
{
    "type": "inlineTime",
    "data": [
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
    ]
}
```

#### ServerPermission

```json
{
    "type": "serverPermission",
    "data": {
        "id": "portal1",
        "failureMode": "fallBack",
        "fallBack": {
            "type": "inlineTime",
            "data": [
                {
                    "days": ["Mo","Tu","We","Th","Fr"],
                    "periods": [
                        {
                            "start": "09:00:00",
                            "end": "13:00:00"
                        },
                        {
                            "start": "14:00:00",
                            "end": "18:20:00"
                        }
                    ]
                }
            ]
        }
    }
}
```

This gate gets the decision on whether it is active from the server. The server is presented with the following information to come to a decision:

- Request Id (id given to the gate)
- User Id
- Token Id
- Token Number
- Originating Entity (normally a reader or action)
- Target Entity (normally a portal or output)
- Command Name (e.g. `RequestEntry`, `ChangeMode`)

The recommended data to use on coming to a decision is the `RequestId` and `UserId`.

##### failureMode

**[enum] (fallBack)** Specifies the failure mode, if the controller can not get an answer from the server.

- **allow**
- **deny**
- **fallBack**

##### fallBack

**[permissionGate] (optional)** Specifies the permissions to use if the server permission request fails.

In the event of the server being unavailable, the `failureMode` gate parameter is used to determine what the gate does. If the failure mode is set to `fallBack`, the fallBack permission gates are used.

>The use of this gate is likely to be observed by the user as a delay between the read of the token and the resultant indication. This will be very apparent if the server is off-line, when a  delay of couple of seconds  may be possible.

Please see the [Permission Request](..\api\PermissionRequest.md) document for information on the protocol.

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

