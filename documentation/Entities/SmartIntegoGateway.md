# SmartIntego.Gateway

**Supported >= 2.0.0**

Defines the connection parameters for an individual Gateway node.

```json
{
    "controller": {
        "smartIntego": {
                "gateways": [
                    {
                        "id": "Gateway1",
                        "ipAddress": "192.168.1.110",
                        "bus": 10,
                        "encryptionKey": "p@ssw0rd"
                    }
                ]
            }
        }
}
```

## Properties

### ipAddress

**[string(100)] [required]** The IP address of the gateway node.

### bus

**[uint] [required]** The bus prefix that will be used to link [SmartIntego.Readers](SmartIntegoReader.md) to this gateway.

### encryptionKey

**[string(16)] [optional]** The AES encryption key, if use.

## States

### Online

**[boolean]** True if the gateway is communicating with the controller.

## Events

## Commands
