# Commands

When a Command needs to be sent to a controller it is queued on the service to
the commands queue. The queue uses the standard server
queue semantics to push message to the client.

## Queue URL

~/device/`deviceSerialNumber`/commands/messages

## URL Parameters

### Required

`deviceSerialNumber=[string]`