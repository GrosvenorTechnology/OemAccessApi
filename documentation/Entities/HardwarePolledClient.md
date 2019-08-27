# Hardware.PolledClient

**Preview >= 4.1.0**


The polled client has the following operational modes:

- **normal** (Default) – Normal polled operation.
- **disabled** - Polling to client controller disabled.

Please see the [Operational Mode Overview](../ApplicationConfiguration/ModeOverview.md) document for more information.

````json
{
    "id": "OEM-ADV-C2-SNG~00001679_Poller",
    "operationalMode": "Normal",
    "serialNumber": "OEM-ADV-C2-SNG~00001679",
    "sharedKey": "GOf49h+tyjUckJ6KMj61KbpKmQSiAkX9/kdNhBQheRY="
}
````

## Properties

### operationalMode

**[enum] (normal)** Specifies which operational mode is the default.

### serialNumber

**[string]** Specify the client controller's serial number.

### sharedKey

**[string]** Specify the client controller's shared key.

### protocolParameters

**[string[]] (empty)** Used by support to change the protocol set-up.

### changeModePermissions

**[string[]] (empty)** A person with a permission on this list can execute the `ChangeMode` command.

## States

### OperationalMode

**[enum]** This shows the current operational mode for the output.

### Online

**[bool]** Whether the client controller is on-line.

## Events

No standalone events.

## Commands

No standalone commands.
