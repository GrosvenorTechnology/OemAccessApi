# Permission Gates

Permission Gates are used to determine whether a user has permission to carry out a command, most often used to unlock a door.

The gates can be defined in a `User` record (under `permissions`) or in a `Shared Permission Set` record. The `Shared Permission Set` records can be referred to by using the `sharedPermissionSet` gate under `permissions` property in the `User` record.

The following types of gates are currently supported:

## Always

```json
{
    "type": "always"
}
```

The `always` gate is always active. Not strictly required, used for test purposes.

## SystemMode

> was `mode` - will still work, but now depreciated to avoid confusion with operational modes.

```json
{
    "type": "systemMode",
    "data": "Emergency"
}
```

Checks if the system mode referenced in the data section is currently active.  The data section is expected to be an EntityKey in the format Namespace.Type:Id.  If only the Id is provided then Common.SystemMode will be assumed as the default for the entity type.

## Not

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

## And

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

## Or

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

## TimePeriod

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

## Time

```json
{
    "type": "time",
    "data": "Mon-Fri-9-17"
}
```

Checks if the time table referenced in the data section is currently active.  The data section is expected to be an EntityKey in the format Namespace.Type:Id.  If only the Id is provided then Common.TimeTable will be assumed as the default for the entity type.

## Holiday

```json
{
    "type": "holiday",
    "data": "BankHolidays"
}
```

Checks if the holiday table referenced in the data section is currently active.  The data section is expected to be an EntityKey in the format Namespace.Type:Id.  If only the Id is provided then Common.Holiday will be assumed as the default for the entity type.

## InlineTime

There are two ways of defining an inline time:

### Method 1

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

### Method 2

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

## SharedPermissionSet

**Supported >= 4.3.0***

```json
{
    "type": "sharedPermissionSet",
    "data": "SharedPerm1"
}
```

This gate allows you to refer to a record which has gates defined in it. The advantage of this is that if you want to define the same gates for many users, you can create one of these records and point to it using this gate. Then any changes to the gates in this record, will alter the permissions of all the users that use the record.

## ServerPermission

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

### failureMode

**[enum] (fallBack)** Specifies the failure mode, if the controller can not get an answer from the server.

- **allow**
- **deny**
- **fallBack**

### fallBack

**[permissionGate] (optional)** Specifies the permissions to use if the server permission request fails.

In the event of the server being unavailable, the `failureMode` gate parameter is used to determine what the gate does. If the failure mode is set to `fallBack`, the fallBack permission gates are used.

>The use of this gate is likely to be observed by the user as a delay between the read of the token and the resultant indication. This will be very apparent if the server is off-line, when a  delay of couple of seconds  may be possible.

Please see the [Permission Request](..\api\PermissionRequest.md) document for information on the protocol.
