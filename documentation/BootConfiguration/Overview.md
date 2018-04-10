# Boot Configuration

The Boot configuration are loaded from a USB key on boot. The Key should be
FAT32 formatted and present in the controller at power on. The file bootconfig.json
should be in the root of the key. If a filename \<serialNumber\>.bootconfig.json, where
the \~ in the serial number is replaced with \_ (i.e.
SAT-ADV-C-MLT_00004562.bootconfig.json) that matches the serial number of the controller,
this will be loaded instead of boot.config.

```json
{
    "boot": {
        "defaultUri": "http://mgmt.gtl.net/grosvenor-oem",
        "services": [
            { "name": "mgmt", "uri": "http://mgmt.gtl.net/grosvenor-oem" }
        ],
        "platformConfig": "device/{deviceSerial}/platformConfiguration",
        "sharedKey": "9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY=",
        "customHeaders": [
            { "name": "deviceId", "value": "myCustomDeviceId"},
            { "name": "token", "value": "myCustomToken"}
        ],
        "network": {
            "dhcpEnabled": true
        }
    }
}

```

## Default URI

[required] This URI will be used as the base for all default URIs and relative
custom URIs.

## Platform Config

[optional] This is the URI that is used to load the platform config, if present
{deviceSerial} will be replaced with the devices serial number.

## Services

[optional] To help keep config files a bit cleaner, it is possible to specify
alias for services. These aliases can be used in any URI in the form
{myService}.

## Shared Key

[required] This is the pre-shared key that will be used to sign all requests
using a SHA256 HMAC. It should be 32bytes of random data encoded in Base64.

## Custom Headers

[optional] Any extra static information can be added to the header section of
all HTTP requests.

## Network

[optional] Configure if the device should use a dhcp or static IP address. The
device will default to DHCP.

TODO – static network config to be added.
