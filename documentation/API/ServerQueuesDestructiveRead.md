# Receive and Delete Messages (Destructive Read)

This operation receives a message from a queue or subscription, and removes the
message from that queue or subscription in one atomic operation.

## URL

~/`queuename`/messages/head?`count=100`

## Method

`DELETE`

## Header Params

### Required

`Authorization`

## URL Parameters

### Optional

   `count=[int]` - The maximum number of messages to return. Value must
be greater than zero. If not provided, defaults to 1. The server is free to
enforce its own limits on the number of messages returned.

## Success Response

> **Code:** 200
>
> Messages successfully retrieved and deleted. Data is contained in the JSON
response body
> ````json
> [
>     {
>         "messageId": "3a146f76afee41648677887ffced72d8",
>         "body": {
>             ...
>         }
>     },{
>         "messageId": "59ddbc558edf4c55828737a743f6f52e",
>         "body": {
>             ...
>         }
>     }
> ]
> ````
> The messageId property is a reference to the message in the queue, it only has
meaning in the scope of the selected queue.

OR

> **Code:** 204
>
> No message available for this queue

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

Please note that the OEM Controller will use this method of transferring data by default. If the "NonDestructiveRead" feature is set in the boot config, the `Changes` queue will use the "Server Queue Peek Read".
