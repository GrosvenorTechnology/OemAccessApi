# Post Event

When an event occurs on a controller (i.e. Valid Access) the event is posted
as a standardised event as a JSON payload to this endpoint.

Events in the OEM-Access controller are raised in response to any of three sources:

- The outcome of a command processed on the controller (i.e. Operate an output)
- A change of a monitored state in the controller (an input state changed from reset to active)
- An internal event (i.e. a token was presented to a reader)

All events, regardless of source, follow the same structure, a standard message header, with the contents being a dictionary of Key-Value pairs. The content property will always exist, even if the dictionary is empty.

## URL

/grosvenor-oem/device/\<DeviceSerialNumber\>/events

## Method

`POST`

## Header Params

### Required

`Authorization`

## Data Parameters

````json
{
    "messageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
    "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
    "previousMessageId": null,
    "timestamp": "2017-10-1T16:00:00Z",
    "entity": "<source_entity_id>",
    "eventName": "<eventName>",
    "contents": {
        "data1": "value1",
        "data2": "value2"
    }
}
````

## Success Response

> **Code:** 204
>
> The event has been stored.

## Error Response

> ### **Code:** 400 BAD REQUEST
>
> The JSON payload could not be parsed.

OR

> ### **Code:** 401 UNAUTHORIZED
>
> Authorised header not supplied

OR

> ### **Code:** 403 FORBIDDEN
>
> Supplied authorisation credentials does not allow access to this resource

OR

> ### **Code:** 404 NOT FOUND
>
> Requested resource does not exist, i.e. unknown device serial number

## Samples

The body of the call can be expressed in C# as

````c#
public class Event
{
    public Guid MessageId { get; set; }
    public Guid CorrelationId { get; set; }
    public Guid? PreviousMessageId { get; set; }
    public DateTimeOffset TimeStamp { get; set; }
    public string Entity { get; set; }
    public string EventName { get; set; }
    public bool DiagEvent { get; set; }
    public Dictionary<string,string> Contents { get; set; } = new Dictionary<string, string>();
}
````

## Notes

### Versioning

New entries may be added to the event message schema or to the contents property in future version, therefore it is best practice to ignore unknown properties rather than cause an exception on the unknown data.  Removal of properties will be deemed a braking change.

### Message Contents

Every event generated in the system will have the timestamp field set to the time the system reacted to an event.

Every message sent in the system will have a unique messageId, this is always a guid in the form in the above example.

If the trigger for the originated outside of the system, for example a token presented to reader, then correlationId will contain a new unique guid and previousMessageId will be null.
If the event was triggered as the result of another event inside of the system, e.g. continuing the above example, a token is presented to a reader which results in a ‘Read’ event, the read event could then triggers an access check on a portal, in this case the correlationId is copied from the read event and the messageId of the read event is copied to the previousMessageId of the access check event.

Every event originates from a source entity, this value is set in the entity field, it is always of the form `<namespace>.<entityType>:<entityId>`, for example `AccessControl.Reader:FrontDoor`.
Every event has name, this is set in the eventName property.

Every event will have zero or more entries in the contents property.  The entries present in the content property are defined in the companion spreadsheet to this document (Controller Events & States).  Content items shown in [] are optional.

### Standard content properties

Most events will contain both a result and a reason value in the content property as a standard way of expressing the outcome of a requested action, e.g. for the RequestEntry event, you might have a result of FailedOnPermissions with a reason of NoPermissions.

A successful read event that resolves to a user would result in the following JSON being sent from the controller to the server

````json
{
    "messageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
    "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
    "previousMessageId": null,
    "timestamp": "2018-03-05T16:00:00Z",
    "entity": "AccessControl.Reader:Front_door",
    "eventName": "Read",
    "contents": {
        "result": "Success",
        "reason": "",
        "tokenData": "6738645732",
        "tokenId": "AccessControl.Token:AGSJG53627",
        "personId": "Common.Person:643784692"
    }
}
````

If a portal was associated to this reader the following access check would be generated if the user did not have access permissions

````json
{
    "messageId": "b67dbc8b-7000-454c-803e-25ca7a9fe088",
    "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
    "previousMessageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
    "timestamp": "2018-03-05T16:00:01Z",
    "entity": "AccessControl.Portal:Front_door",
    "eventName": "RequestEntry",
    "contents": {
        "result": "FailedOnPermissions",
        "reason": "NoPermissions",
        "personId": "Common.Person:643784692"
    }
}
````

### Command Result Events

All commands processed by the controller will result in an event being raised that details the outcome of the command.

For example in the `ChangeMode` command there are two allowable sets of parameters for the command.

- [Reference], Mode, Priority, [Period]
- [Reference]

Using the first parameter set, this will result in the following JSON being sent to the controller:

````json
{
    "messageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
    "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
    "previousMessageId": null,
    "timestamp": "2018-03-05T16:00:00Z",
    "ttl": "00:00:05.00",
    "entity": "AccessControl.Portal:Front_Door",
    "commandName": "ChangeMode",
    "parameters": {
        "mode": "Unlocked",
        "priority": 40,
        "period": "2:00.00",
        "reference": "Common.Person:6386473"
    }
}
````

This will result in an event with the following content when the command is successfully processed.

````json
{
    "messageId": "b67dbc8b-7000-454c-803e-25ca7a9fe088",
    "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
    "previousMessageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
    "timestamp": "2018-03-05T16:00:05Z",
    "entity": "AccessControl.Portal:Front_Door",
    "eventName": "ChangeMode",
    "contents": {
        "result": "Success",
        "reason": "",
        "mode": "Unlocked"
    }
}
````

### State Change Events

All states that change value will raise a state change event.  As an example the PortalState state on the Portal type will result in an event with the following JSON being sent. In the above example extra data on top of the new value of the state is sent in the event.

````json
{
    "messageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
    "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
    "previousMessageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
    "timestamp": "2018-03-05T16:00:05Z",
    "entity": "AccessControl.Portal:Front_Door",
    "eventName": "PortalState",
    "contents": {
        "state": "OpenTooLong",
        "personId": "Common.Person:654738647"
    }
}
````

### Event Sequences

Most events in the system will be stand alone, but in some cases an action results in a chain of events being emitted.

#### Token swipe

In a fully configured system, the swipe of a token will result in potentially the following sequence of events.  Some events will be omitted if for example there is not door sensor installed, or areas are not enabled:

- Reader – Read
- Portal – RequestEntry
- Portal – PortalEntryUsed
- Area – Movement
