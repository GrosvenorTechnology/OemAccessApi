# Command Response & Progress Submission from Line Header (Gateway)

When using controllers in 485 mode, the controllers will forward their command responses to the line header, which will then send these states up to the service.
To improve performance of the line header, messages from all controllers are delivered to the service in batches.

For command responses is allowed to mix command progress and command response messages in the same batch.

## URL

/grosvenor-oem/batch/commands

## Method

`POST`

## Header Params

### Required

`Authorization`

## URL Parameters

### Required

`deviceSerialNumber=[string]`

## Data Parameters

### Command Progress

````json
[
    {
        "serialNumber": "OEM-ADV-C2-SNG~00002003",
        "message":{
            {
                "messageId": "BC6B64D2-14C7-465F-8177-54E2FC046C64",
                "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
                "previousMessageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
                "timeStamp": "2017-10-1T15:39:10Z",
                "progress": "20",
            }
        }
    },
    {
        "serialNumber": "OEM-ADV-C2-SNG~00002003",
        "message":{
            {
                "messageId": "4F57465D-4E05-4BA4-B464-E36FAB4C8903",
                "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
                "previousMessageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
                "timestamp": "2017-10-1T15:39:30Z",
                "result": "FailedOnPermissions",
                "explanation": "NoEnabledAPGs"
            }
        }
    }
]
````

## Success Response

> **Code:** 204
>
> The response has been stored.

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

The structure of the command responses sent to the batch endpoint are the same as for the per device endpoints as described [here](PostCommandResponse.md)

It is the responsibility of the service to validate that the line header that is posting state data for downstream controllers is actually authorised to do so.
