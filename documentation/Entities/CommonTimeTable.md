# Common.TimeTable

Time Tables can be used to control when a user has access to a specific door, when a door is unlocked, or an output operated and can also be used to trigger actions.

Time Tables can be defined in the Application Configuration and in Entity Change messages.

There are also two methods of defining a Time Table:

## Method 1

### In the Application Configuration

````json
{
    "controller": {
        "timeTables": [
        {
            "type": "Common.TimeTable",
            "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
            "description": "Example Time Table",
            "transitions":{
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
        }]
    }
}
````

### In an Entity Change

````json
{
    "entity": {
        "type": "Common.TimeTable",
        "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
        "description": "Example Time Table",
        "transitions":{
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
}
````

## Method 2

This method tends to be more compact when defining regular periods throughout the week.

### In the Application Configuration

````json
{
    "controller": {
        "timeTables": [
        {
            "type": "Common.TimeTable",
            "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
            "description": "Example Time Table",
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
            ]
        }]
    }
}
````

### In an Entity Change

````json
{
    "entity": {
        "type": "Common.TimeTable",
        "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
        "description": "Example Time Table",
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
        ]
    }
}
````

The time table type is used to specify a series of periods based on the day of the week. The number of transitions per day is not limited, but should be kept to a sensible limit of 10 or less. All transitions are processed as local times in the time zone specified in the Device Configuration.

If you require a continuous period that runs over midnight, you must use an end time of 24:00, this will ensure that the period from the first day is seamlessly linked to the next. e.g. Monday 9:00 to Tuesday 17:00, if you build the periods as 09:00-23:59 then 00:00-17:00, there will be a minute discontinuity at 23:59 to midnight on Monday where the timeTable will be inactive.  Instead use 09:00-24:00 and 00:00-17:00.

## States

### TimeTableState

**[enum]** The state shows the current state of the time table.

- InPeriod
- OutOfPeriod

## Events

### TimeTablePeriod

**[enum]** The event reports the transition of the time table period.

- Started
- Ended
