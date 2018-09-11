# Common.Actions

**Supported >= 3.0.0**

Actions provide a way for events fired locally on a controller to be mapped to commands on the same controller.
This provides a way to link cause and effect locally on a controller, e.g. unlock one or more portals when a
fire alarm input is active and re-lock the portals when the alarm clears.

The input has the following operational modes:

- **enabled** (Default) – The Action will trigger when matching events are received.
- **disabled** - The Action will not trigger.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

````json
{
    "controller": {
        "actions": [{
                "id": "UnlockOnFireAlarm",
                "sourceType": "Hardware.Input",
                "sourceIds": ["FireAlarm"],
                "targetType": "AccessControl.Portal",
                "targetIds": ["FrontDoor-East", "FrontDoor-West"],
                "map": [
                    {
                        "event": "InputState:active",
                        "command": "ChangeMode",
                        "arguments":
                        {
                            "Mode": "Unlocked"
                        }
                    },
                    {
                        "event": "InputState:inactive",
                        "command": "ChangeMode"
                    }
                ],
                "restoreCommand":
                {
                    "command": "ClearOwnedFromModeStack"
                }
            }
        ]
    }
}
````

The above example is taken from the [Fire Alarm Action](../Samples/ApplicationConfiguration/FireAlarmAction.json) sample
which is designed to work with a Door Blade in slot 1.  The example shows how to wire up a single input to
unlock multiple doors.

## Properties

### operationalMode

**[enum] (enabled)** Specifies which operational mode is the default.

### sourceType

**[string] [required]** Specifies the type entities (Inputs, readers etc.) that the action will trigger on.

### sourceIds

**[string[]] (empty)** A list of Ids that the event(s) come from. If you want the `Action` to trigger of all entities of the source type, you can provide the single Id of `"*"`.  If no Ids are provided, the `Action` will not fire.

### targetType

**[string] [required]** Specifies the type of entities that this `Action` will send commands to.

### targetIds

**[string[]] (empty)** A list of entity Ids that the command will be sent to.  These Ids must all be of the same type as `targetType`.  If no Ids are provided the `Action` will not fire.

### ReferenceFrom

**[enum] (source)** Where to get the reference property used in `ChangeMode` commands from.

- source
- person
- action

This can bee seen in action by looking at a more [advanced](../Samples/ApplicationConfiguration/FireAlarmActionAdvanced.json) form of the fire alarm example. In this example both inputs on the door blade are being used to unlock the portals.  When a `ChangeMode` command is sent, it requires a reference so it can be cancelled later.  When the `ReferenceFrom` is set to `source` the Id of the source of the event is used, this means that both inputs place their own unlock command into the 'mode stack' of each portals, so the portal remains unlocked until both inputs are reset.  If however the `ReferenceFrom` property is changed to `action` the reference used is the Id of the action itself, this means that only a single unlock command is placed in each portals stack; when the first input activates the portal's mode is changed with a reference of `UnlockFireAlarm` and the when the second input activates it see that the portals already have an unlock command with the reference `UnlockFireAlarm` so that is updated with any new settings but is left as the only entry.  When the first input then resets that single reference is removed and the door re-locks.  This allows you to tune the behaviour of the `Action`, in the case of our fire alarm example you probably want to use `source` so the doors remain unlocked until all inputs are cleared.

The third option here `person` allows you to use use the identity of a person (user) associated to an event, such as a `Read` event from a `Hardware.Reader`.  In [this](../Samples/ApplicationConfiguration/ReaderCommandsUnlockPortal.json) example we show how to wire up reader keypad events to be used to unlock portals.  The 1 event unlocks the doors, the 2 event re-locks, or more precisely removes the unlock for the user. As the `ReferenceFrom` is the Id of the user **not** the reader, multiple people can unlock the portal, but and they must all re-lock the portal before it will finally close.  Again in this case they could change the `ReferenceFrom` to be `action` then any number of users could unlock the portal and the first user to re-lock the portal would cause it to actually lock.

### PermissionsFrom

**[enum] (none)** Which entity type to use for permission. A value of `none` will bypass any required permissions. This is an advanced use case and we suggest you always use none unless the action uses read events from a reader.

- source
- person
- action
- none

The option `person` allows you to only allow certain people (with permission) to use the action. This is required, for instance, when using a `Read` event from a `Hardware.Reader`.  In [this](../Samples/ApplicationConfiguration/ReaderCommandsUnlockPortal.json) example we show how to wire up reader keypad events to be used to unlock portals. The 1 event unlocks the doors, the 2 event re-locks. Using the option `none` would allow anybody (including people without normal access to the doors) to unlock/relock the doors. As the `PermissionsFrom` is set to `person`, the commands sent by the action to the doors, instructs the doors to try and match one of the door's `changeModePermissions` with one of the person's (User) permissions. If this is successful, the command succeeds and the doors are unlocked/relocked.

### Event To Command Mappings (map)

**[eventToCommand[]] (empty)** The `map` property allows you to define how events from the source are mapped to individual commands on the target.  In our example we are using an input to unlock portals, so we want to map changes in the `InputState` to the `ChangeMode` command for the portals.

#### event

**[string] [required]** Specifies the event to match from the source.  The format of this property varies depending on the type of event.  If the source event is a `StateChange` Event, such as the input in this example, the format is `<eventname>:<state>` e.g. `InputState:active`.  For other cases, if the event contains a `Result` property, then the format is `<eventname>:<result>` e.g. `Read:success` from a reader, otherwise its just the event name.

#### command

**[string] [required]** Specifies the name of the command on the target to trigger.

#### arguments

**[[string, object]] (empty)** The arguments you wish to add to the command when it is sent to the target.

### RestoreCommand

**[commandDefinition] (empty)** This allows you to specify a command to be issued when an Action has been disable.

#### command

**[string] [required]** Specifies the name of the command on the target to trigger.

#### arguments

**[[string, object]] (empty)** The arguments you wish to add to the command when it is sent to the target.

## States

### OperationalMode

**[enum]** This shows the current mode (see Input Operational Modes for
details).

## Events

None.

## Commands

No standalone commands.