# Using a read to operate outputs

## Scenario

This is an example of using a read from a reader to operate output(s). A person with relevant permissions will operate the required outputs.

## Implementation

The full JSON implementation can be seen [here](ReadOperatingOutputs.json).

Below we explain how each element works.

### Reader

Define a reader:

```json
      "readers": [
        {
          "tokenFormatType": "RDSATEONPRO",
          "address": "1-0-1",
          "id": "Reader-101",
          "description": "Reader 101"
        }
```

Nothing special here!

### Outputs

Define one or more outputs:

```json
      "outputs": [
        {
          "operationalMode": "Off",
          "address": "1-0-1",
          "changeModePermissions": [
            "Output101"
          ],
          "id": "Output-101",
          "description": "Output 101"
        },
        {
          "operationalMode": "Off",
          "address": "1-0-2",
          "changeModePermissions": [
            "Output102"
          ],
          "id": "Output-102",
          "description": "Output 102"
        }
      ],
```

The thing to note here is that the `operationalMode` of the outputs is set to `Off`, and `changeModePermissions` for each output has been given a permission name. If a user has that permission (and the gate(s) for that permission are active), the output will operate.

### Action

The action ties the reader to the outputs:

```json
      "actions": [
        {
          "sourceType": "Hardware.Reader",
          "sourceIds": [
            "Reader-101"
          ],
          "targetType": "Hardware.Output",
          "targetIds": [
            "Output-101",
            "Output-102"
          ],
          "permissionsFrom": "Person",
          "map": [
            {
              "event": "Read:success",
              "command": "ChangeMode",
              "arguments": {
                "mode": "On",
                "period": "00:00:05"
              }
            }
          ],
          "restoreCommand": {
            "command": "ClearOwnedFromModeStack",
            "arguments": {}
          },
          "id": "Action-1",
          "description": "Operate Outputs Action"
        }
      ],
```

A successful read will be picked up by this action and pass a `ChangeMode` command to the defined outputs. Because the `permissionsFrom` is set to `Person`, the users permissions are given to each output. If the user has the permission for the output, the output will operate (for 5 seconds).

## Conclusion

This method can be adapted to many situations and other entity types could be used instead of outputs. We have chosen here to change the operational mode of the outputs to get them to operate (this is the recommended way), but you could use the `OperateForPeriod` command instead and set the `commandPermissions` property to set the permission.
