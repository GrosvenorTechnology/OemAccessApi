# Entity DeleteAll

When an entity delete all message is sent over the Entity Change Queue, the
controller will delete all entity instances of the specified type.

````json
{
    "deleteall": {
        "type": "AccessControl.User"
    }
}
````