# AccessControl.PortalInterlock

**Supported >= 3.1.0**

The Portal Interlock has the following operational modes:

- **enabled** (Default)
- **lockDown**
- **disabled**

When two or more portals are allocated to a portal interlock, those portals obey the portal interlock rules:

- If in the `enabled` mode, only one portal can be open at a time. While a portal is open, the other(s) will be prevented from opening, either by denying access requests or, if scheduled `unlocked`, temporarily locking the portal(s).

- The `lockDown` mode prevents any portals from opening.

- The `disabled` mode allows the portals to open with no interlock restrictions.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

````json
{
    "controller": {
        "portalInterlocks": [{
                "id": "cleanRoomAirlock",
                "operationalMode": "enabled",
                "changeModePermissions": ["operator"]
            }
        ]
    }
}
````

## Properties

### operationalMode

**[enum] (enabled)** Specifies which operational mode is the default.

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the `ChangeMode` command.

## States

### OperationalMode

**[enum]** This shows the current operational mode.

- **enabled** (Default)
- **lockDown**
- **disabled**

### InterlockState

**[enum]** State of the Interlock.

- **idle**
- **interlocked**
- **breached**

## Events

No standalone events.

## Commands

No standalone commands.