# Server Queues

All queues in the APIs (Level 1 & 2) used to push messages from the server to
the client follow the following semantics, the client can only receive from
these queues.

The server should notify the controller that data is available on the queue by returning the queue's activity name via the [Device Heartbeat](DeviceHeartbeat.md) response.

The Queues are only intended to have a single consumer

## Queue API

All endpoints that implement the Server Queue pattern will be required to implement the following endpoints.

1. [Destructive Read](ServerQueuesDestructiveRead.md)
2. [Peek](ServerQueuesPeekRead.md)
3. [Delete](ServerQueuesDelete.md)
