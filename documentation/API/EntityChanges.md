# Entity Changes

The entity change queue is used to send update to the controller when an entity
is changed in the service.

## Queue URL

/grosvenor-oem/device/`DeviceSerialNumber`/changes/messages

## URL Parameters

### Required

`deviceSerialNumber=[string]`

## Message types

The following message types will be returned via the changes queue

- [External-Ref::Oem-Controller-DomainModel::AccessControlUser]

- [External-Ref::Oem-Controller-DomainModel::CommonTimeTable]

- [External-Ref::Oem-Controller-DomainModel::EntityReconcile]

- [External-Ref::Oem-Controller-DomainModel::EntityDelete]
