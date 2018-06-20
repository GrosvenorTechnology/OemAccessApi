# SmartIntego.Reader

**Supported >= 2.0.0**

A reader is the logical representation of a token reader integrated into a SmartIntego Lock, and as a result can only be used with SmartIntego Portals.

A reader can be configured to be in a specific operational mode. The
reader can be commanded to change its mode at any time, a trigger based on a
time schedule would be a common example. This property sets the default mode the
reader will operate in. The available operational modes are:

- **tokenOnly** – Only the token number is required (default).
- **disabled** – All reads from the reader will be ignored.

````json
{
    "controller": {
        "readers": [{
                "id": "BoardRoom-Entry",
                "type": "SmartIntego.Reader",
                "address": "10-0-600"
            }
        ]
    }
}

````

The type name for readers is Hardware.Reader

## Properties

### type

**[EntityType] [required]** This setting tells the system this what type of reader this is, the default is `Hardware.Reader`. This is required and must be set to `SmartIntego.Reader` to tell the controller this is an SmartIntego reader.

### address

**[hardware-address]** The logical address for the reader.  For SmartIntego the first number is the bus number, which links a reader to a gateway, the second is always zero and the third is the device ID in **hex**.

## States

## Events

The following events are sent from the reader. All events have the reader
identity as their event source. Each event carries extra data in its payload,
which is listed with each event.

### Read

A token was presented to the reader.

The read event has a discriminator (Result) that describes the outcome of the
read. The other items included in the event payload will depend on the value of
the Result. These include:

- **TokenData [string]** – A textual representation of the data from the reader.
- **TokenId [identifier]** – if the value read from the reader could be matched to
    a value in the database, this is the unique identifier for that token.
- **PersonId [identifier]** – The identifier for the person the token was
    associated with

The following table shows which items are present for each result, values in
optional in these events.

| **Result**       | **Event Content**                |
|------------------|----------------------------------|
| Success          | TokenData, TokenId, PersonId     |
| UnknownToken     | TokenData                        |
| UnknownPerson    | TokenData, TokenId               |
| TokenNotEnabled  | TokenData, TokenId, PersonId     |
| PersonNotEnabled | TokenData, TokenId, PersonId     |
| ReaderDisabled   | TokenData, [TokenId], [PersonId] |
| ReadDecodeFailure |                                 |
| ReadInfoDecodeFailure |                             |
| LockNotLicensed | PHI                               |

### LocalVerificationRead

A token was read and found on the whitelist with the local verification flag set. The lock was automatically unlocked.

| **Result**       | **Event Content**                |
|------------------|----------------------------------|
| Success          | TokenData, TokenId, PersonId     |

### UserUnlocked

| **Result**       | **Event Content**                |
|------------------|----------------------------------|
| Success          | TokenData, TokenId, PersonId     |

### UserLocked

| **Result**       | **Event Content**                |
|------------------|----------------------------------|
| Success          | TokenData, TokenId, PersonId     |

### Unlocked

| **Result**       | **Event Content**                |
|------------------|----------------------------------|
| Success          |                                  |

### Locked

| **Result**       | **Event Content**                |
|------------------|----------------------------------|
| Success          |                                  |

## Commands

### ChangeMode

Add or remove an entry from the operational mode stack of the reader.

Add Entry to stack

- **Mode [entityId]** - The mode to change to.
- **Priority [int]** - The priority for the mode entry.
- **Period [timespan] (optional)** - If provided the entry will be automatically removed after the given time period.
- **Reference [string] (optional)** - A reference that can be used to remove the entry from the stack.

Remove entry from stack

- **Reference [string] (optional)** - Remove the entry with the matching reference from the stack.

Depending on the result of the command the following items will be present in the
event contents.

| **Result**           | **Reason**            |   **Event Content** |
|----------------------|-----------------------|---------------------|
| Success              |                       | [Mode]              |
| FailedOnPermissions  | NoPermissions         | [Mode]              |
|                      | NoRelevantPermissions | [Mode]              |
|                      | NoActivePermissions   | [Mode]              |
| CommandArgumentError |                       | [Mode]              |
