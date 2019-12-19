# Common.Holiday

**Supported >= 3.2.0**.

Holidays can be used by permission gates, time-tables and actions. They can be controlled by changing their operational mode, either directly or indirectly by using actions.

A holiday has the following operational modes:

- **normal** (default)
- **disabled**

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

Holidays can be defined in the Application Configuration and in Entity Change messages:

- In the Application Configuration

````json
{
    "controller": {
        "holidays":[
        {
            "id": "BankHolidays",
            "description": "Bank Holidays 2018",
            "holidayPeriods":[
            {
                "start": "2018-11-15T12:00:00",
                "end": "2018-11-16"
            },
            {
                "start": "2018-12-25",
                "end": "2018-12-26"
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
        "type": "Common.Holiday",
        "id": "BankHolidays",
        "description": "Bank Holidays 2018",
        "holidayPeriods":[
        {
            "start": "2018-11-15T12:00:00",
            "end": "2018-11-16"
        },
        {
            "start": "2018-12-25",
            "end": "2018-12-26"
        }
        ]
    }
}
````

## Properties

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the `ChangeMode` command.

### holidayPeriods

**[holidayPeriod[]] (empty)** A list of one or more holiday periods.

#### holidayPeriod

##### start

**[datetime]** The start time of the holiday period.

##### end

**[datetime]** The end time of the holiday period.

>N.B. When defining the a datetime without the time element, the time will default to 00:00:00. So an end time that stops at midnight would need to have the date of the following day.

## States

### OperationalMode

**[enum]** This shows the current operational mode for the holiday.

- **normal** (default)
- **disabled**

### HolidayState

**[enum]** The state shows the current state of the holiday.

- InPeriod
- OutOfPeriod

>N.B. If the holiday operational mode is set to disabled the holiday state will be set to `OutOfPeriod`.

## Events

### HolidayPeriod

**[enum]** The event reports the transition of a holiday period.

- Started
- Ended

>N.B. If the holiday operational mode is set to disabled the holiday period events will not be created.

## Commands

No Standalone events.
