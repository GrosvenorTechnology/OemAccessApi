# Hardware.Controller

The controller type is the base of the deviceConfig.json file that will
configure the AccessControl application on the controller.

````json
{
    "controller": {
        "id": "Ctrl-46537453278",
        "description": "Reception",
        "timezone": "Europe/London",
        "areaMode": "local"
    }
}
````

## Properties

### Timezone

**[string (200)] (Etc/UTC)** The IANA timezone to be used to evaluate local time.

### AreaMode

**[enum] (local)** Sets the mode for area processing. Acceptable values are:

- local – All area processing is handled locally on the controller;
    the server is responsible for distributing area state to all controllers.
- remote – All error movement will be validated on the server. All local
    functions such as max occupancy will be disabled.

## States

## Events

## Commands
