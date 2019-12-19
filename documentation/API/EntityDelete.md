# Entity Delete

When an entity delete message is sent over the Entity Change Queue, the
controller will delete the entity instances of the specified type with the
matching id.

````json
{
    "delete": {
        "type": "AccessControl.User",
        "id": "26b57916-5a63-4c2e-a9ac-6502ed567a2d"
    }
}
````
