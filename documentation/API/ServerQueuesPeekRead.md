# Peek Messages (Non-Destructive Read)

This operation receives up to the requested number of messages from a queue, it
does not remove the messages from that queue. A subsequent read from the queue
will return the same messages until the are explicitly deleted using the
messageIds provided in the response.

If a list of IDs is supplied in the request body, these messages will be cleared
from the queue before the requested messages are read.

## URL

~/`queuename`/messages/head?`count=100`

## Method

`POST`

## Header Params

### Required

`Authorization`

## URL Parameters

### Optional

`count=[int]` - The maximum number of messages to return. Value must
be greater than zero. If not provided, defaults to 1. The server is free to
enforce its own limits on the number of messages returned.

## Data Parameters

````json
[
    "3a146f76afee41648677887ffced72d8",
    "59ddbc558edf4c55828737a743f6f52e"
]
````

## Success Response

> **Code:** 200
>
> Messages successfully retrieved. Message is contained in the JSON response body

````json
[
    {
        "messageId": "3a146f76afee41648677887ffced72d8",
        "body": {
            ...
        }
    },{
        "messageId": "59ddbc558edf4c55828737a743f6f52e",
        "body": {
            ...
        }
    }
]
````

The messageId property is a reference to the message in the queue, it only has
meaning in the scope of the selected queue.

OR

**Code:** 204

No message available for this queue

## Error Response

> ### **Code:** 400 BAD REQUEST
>
> Bad request, i.e. count query string parameter is not valid.

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
> Requested resource does not exist, i.e. unknown device serial number or
queue.

## Notes

Please note that the OEM Controller will only use this method of transferring data for the `Changes` queue, if the "NonDestructiveRead" feature is set in the boot config. Other queues use the "Server Queue Destructive Read".
