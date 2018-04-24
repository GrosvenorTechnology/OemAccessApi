# Common.TimeTable

Time schedules can be used to control when a user has access to a specific door,
when a door is unlocked, or an output operated. The basic time table type is
used to specify a series of transitions based on day of week, the number of
transitions per day is not limited, but should be kept to a sensible limit of 10
or less. All transitions are processed as local times in the time zone specified
in the Device Configuration

````json
{
    "entity": {
        "type": "Common.TimeTable",
        "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
        "description": "Example Time Table",
        "transisitions":{
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

If you require a continuous period that runs over midnight, you must use an end time of 24:00, this will ensure that transition from the first day is seamlessly linked to the next. e.g. Monday 9:00 to Tuesday 17:00, if you build the transitions as 09:00-23:59 then 00:00-17:00, there will be a second discontinuity at 23:59 to midnight on Monday where the timeTable will be inactive.  Instead use 09:00-24:00 and 00:00-17:00.