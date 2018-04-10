# Invokers

````json
{
    "controller": {
        "invokers": [{
            "trigger": {
                "entity": "AccessControl.ReaderCommand:CMD-1",
                "event": "Command",
                "payload": {
                    "command": "\@InRange(3000,3999)"
                }
            },
            "actions": [{
                "entity": "AccessControl.Portal:P-1",
                "command": "ChangeOperationalMode",
                "payload": {
                    "mode": "Unlocked",
                    "period": "60:00.00",
                    "priority": 100,
                    "referenceId": "\@Trigger.Entity"
                }
            }]
        }]
    }
}
````