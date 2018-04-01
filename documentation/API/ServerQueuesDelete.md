# Delete messages

Removes the messages with the specified Ids from the queue. The request will
return a success response even if the message Ids can not be found.

## URL

~/`queuename`/messages

## Method

`DELETE`

## Header Params

### Required

`Authorization`

## Data Parameters

````json
[
    "3a146f76afee41648677887ffced72d8",
    "59ddbc558edf4c55828737a743f6f52e"
]
````

## Success Response

> **Code:** 204
>
> The requested messages were deleted.

## Error Response

> ### **Code:** 400 BAD REQUEST
>
> Bad request, i.e. the request body can’t be parsed.

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
