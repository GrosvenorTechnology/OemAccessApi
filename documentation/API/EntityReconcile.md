# Entity Reconcile

When an entity reconcile message is sent over the Entity Change Queue, the
controller will check that all entity instances of the specified type stored on
the controller are present in the key list. If the instance is not in the key
list it will be deleted from the controller.

````json
{
    "reconcile": {
        "type": "AccessControl.User",
        "keys": [
            "26b57916-5a63-4c2e-a9ac-6502ed567a2d",
            "eb0d39b0-8746-418f-82b9-af0509ead741",
            "e1664e3e-0cbe-47f8-aa01-2eecc89dcafa"
        ]
    }
}
````
