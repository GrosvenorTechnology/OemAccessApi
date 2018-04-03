# Controller

The controller type is the base of the deviceConfig.json file that will
configure the AccessControl application on the controller.

````json
{
    "controller": {
        "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
        "description": "Controller 1",
        "timezone": "Europe/London",
        "areaMode": "remote"
    }
}
````

## Properties

### Timezone

[string (200)] The IANA timezone to be used to evaluate local time.

### AreaMode

Sets the mode for area processing. Acceptable values are:

- local (default) – All area processing is handled locally on the controller;
    the server is responsible for distributing area state to all controllers.
- remote – All error movement will be validated on the server. All local
    functions such as max occupancy will be disabled.

## States

## Events

## Commands
