# Post Batched Events

When using controllers in 485 mode, the controllers will forward their events to the line header, which will then send these events up to the service.
To improve performance of the line header, messages from all controllers are delivered to the service in batches. 

Events in the OEM-Access controller are raised in response to any of three sources:

- The outcome of a command processed on the controller (i.e. Operate an output)
- A change of a monitored state in the controller (an input state changed from reset to active)
- An internal event (i.e. a token was presented to a reader)

All events, regardless of source, follow the same structure, a standard message header, with the contents being a dictionary of Key-Value pairs. The content property will always exist, even if the dictionary is empty.

## URL

/grosvenor-oem/batch/events

## Method

`POST`

## Header Params

### Required

`Authorization`

## Data Parameters

````json
[
    {
        "serialNumber": "OEM-ADV-C2-SNG~00002003",
        "message":{
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
        }
    },
    {...},
    {...}
]
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


## Notes

The structure of the events sent to the batch endpoint are the same as for the per device endpoints as described [here](PostEvent.md)

It is the responsibility of the service to validate that the line header that is posting event data for downstream controllers is actually authorised to do so.
