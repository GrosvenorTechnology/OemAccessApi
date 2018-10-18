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
                        "address": "10-0-100",
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

### address

> Introduced after version 3.0.21739

**[hardware-address]** The logical address for the gateway.  For SmartIntego the first number is the bus number, which is used to link [SmartIntego.Readers](SmartIntegoReader.md) to this gateway, the second is always zero and the third is the device ID in **hex**.

### bus

> Depreciated after version 3.0.21739, use address

**[uint] [obsolete]** The bus prefix that will be used to link [SmartIntego.Readers](SmartIntegoReader.md) to this gateway.

### encryptionKey

**[string(16)] [optional]** The AES encryption key, if use.

## States

### Online

**[boolean]** True if the gateway is communicating with the controller.

## Events

## Commands
