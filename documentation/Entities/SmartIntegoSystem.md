# SmartIntego.System

**Supported >= 2.0.0**

The SmartIntego System object is used to define common properties across all components of the SmartIntego system. 

```json
{
    "controller": {
        "smartIntego": {
                "id": "SI",
                "physicalHardwareIds": ["0E5A3H", "0E839K", "0E84BM", "0F1P5S", "0F1R10", "0F1P44"],
                "tokenFormatType": "SmartIntego",
                "whitelistUpdatePeriod": "01:00:00",
                "gateways": [...]           
            }
        }
}
```

## Properties

### physicalHardwareIds

**[string(10)[]] (empty)** A list of all device physical hardware Ids (PHI) to be used. Devices who's PHI does not appear in this list will be ignored.  This is required for SimonsVoss certification. 

### tokenFormatType

**[string (100)] ** The name of tokenFormat to use, there is no default. Use the
`controller.tokenFormats` section in the application config to define the required format. Only one format can be supported across all SmartIntego readers.

## whitelistUpdatePeriod 

**[timespan] (01:00:00)** The time of day that scheduled white list updates will occurs.

## gateways

**[[SmartIntego.Gateway](SmartIntegoGateway.md)[]] (empty)** A list of gateway definition objects. Current only a single gateway is supported.

## States

## Events

## Commands

For a command to be successful, it requires permission. There is a list of
security contexts for each command. These are linked to permissions. Providing
one comes back positive, the command is allowed.

If a command is marked is *internal,* then it is available only within the
controller but will still raise events.

### UpdateWhitelist

Forces all white lists to be re-evaluated and changes sent to locks.

| **Result**           |   **Event Content** |
|----------------------|---------------------|
| Success              |                     |
| FailedBecauseOfError |                     |