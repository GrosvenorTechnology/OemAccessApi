# Boot Configuration

The Boot configuration are loaded from a USB key on boot. The Key should be FAT32 formatted and present in the controller at power on. The file bootconfig.json should be in the root of the key. If a filename \<serialNumber\>.bootconfig.json, where the \~ in the serial number is replaced with \_ (i.e. SAT-ADV-C-MLT_00004562.bootconfig.json) that matches the serial number of the controller, this will be loaded instead of boot.config.

```json
{
    "boot": {
        "defaultUri": "http://mgmt.gtl.net/grosvenor-oem",
        "proxy": {
            "uri": "http://proxyserver:80",
            "userName": "proxyUser",
            "password": "password123",
            "bypassProxyOnLocal": false
        },
        "services": [
            { "name": "mgmt", "uri": "http://mgmt.gtl.net/grosvenor-oem" }
        ],
        "platformConfig": "device/{deviceSerial}/platformConfiguration",
        "sharedKey": "9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY=",
        "customHeaders": [
            { "name": "deviceId", "value": "myCustomDeviceId"},
            { "name": "token", "value": "myCustomToken"}
        ],
        "features": [
            "EventContentExtension",
            "TimeTableEndAdjustment"
        ],
        "network": {
            "eth0": {
                "address": "192.168.43.101",
                "netmask": "255.255.255.0",
                "gateway": "192.168.43.254"
            },
            "primaryDns": "192.168.43.1",
            "secondaryDns": "192.168.42.4",
            "ntpServer": "pool.ntp.org",
            "sshEnabled": true
        }
    }
}

```

## Default URI

**[string] [required]** This URI will be used as the base for all default URIs and relative
custom URIs.

## Application (>= V4.1.0)

**[string]** If set this string tells the controller what mode it should operate in.  At the moment the valid options are

 - OemAccess (default)
 - OemGateway (Line Header)

## CommunicationsType (>= V4.1.0)

**[string]** This setting is only valid in the OemAccess application, and defines if the device should use network or 485 communications

 - HttpBasic (default)
 - Rs485 

## AutoRestart (>= V4.0.24734)

**[bool] (true)** Controls if the controller will automatically restart when the boot config is updated.  If set to false the boot config will be stored, but controller app must be restarted (not rebooted) to pick up the changes.  This allows more fine grain control of when changes requiring restart are applied. Can also help protect against server errors that send a bootconfig file by mistake.

## BootRs485 (>= V4.1.0)
[optional] Defines communication properties when working in 485 mode

```json
{
    "portSpeed": 19200
}
```

### PortSpeed (>= V4.1.0)

**[int32]** [optional] Sets the baud rate for the 485 port.  Valid values are:

- 9600
- 19200 (default)
- 57500

## Proxy

[optional] Define this section to use a network proxy.

```json
{
    "boot": {
        "proxy": {
            "uri": "http://proxyserver:80",
            "userName": "proxyUser",
            "password": "password123",
            "bypassProxyOnLocal": false
        }
    }
}
```

### Uri

[required] Proxy's URI. This should include its port.

### UserName

[optional] Set if the proxy requires credentials.

### Password

[optional] Set if the proxy requires credentials.

### BypassProxyOnLocal

[optional] (defaults to true) Set to false to use proxy for local addresses.

## Platform Config

[optional] This is the URI that is used to load the platform config, if present {deviceSerial} will be replaced with the devices serial number.  The use of this setting allows you to set the defaultUri to something other than the management platform, and can help make platform configuration files more concise.

## Services

[optional] To help keep config files a bit cleaner, it is possible to specify alias for services. These aliases can be used in any URI in the form
{myService}.

## Shared Key

[required] This is the pre-shared key that will be used to sign all requests using a SHA256 HMAC. It should be 32bytes of random data encoded in Base64.

## Custom Headers

[optional] Any extra static information can be added to the header section of all HTTP requests.

## Features

[optional] Additional non-standard features can be enabled here. Features should only be enabled when recommended by GTL developer support. Current options include:

- **EventContentExtension** - Extends the event content for correlated events.
- **TimeTableEndAdjustment [>=1.0.19635]** - Adds one minute to the end of transitions, allows the use of transitions such as 09:00->17:59 instead of the normal 09:00->18:00.  This only works when times are used in the HH:mm format or using HH:MM:ss and the second component is 00.
- **EntityTypeFallbackExtension [>=2.0.20179]** - Used to convert the entity source of enhanced reader types (i.e. SmartIntego & OSDP) back to the native types.  This extension just remaps the Entity Source of Events and States, it does nothing to normalise events.
- **SiForcedWhitelistTag [>=2.0.20440]** - When enabled every user change will have the tag  `Si-Whitelist` inserted into the tags for every identifier on that user.  Adding or removing this flag will not update users that have already been downloaded to the controller, a change for the user must be sent from the server to update the user record.

## Network

[optional] Configure the network settings.  If this setting is not set or set to `null`, the existing settings will not be changed.

### Static Network Address

To set the controller to use static network settings, the eth0 setting must be set as in the example below. The `gateway` setting is optional, if it is omitted or set to `null`, it's value will not be changed. To clear a setting, set the value to an empty string `""`.

```json
{
    "boot": {
        "network": {
            "eth0": {
                "address": "192.168.43.101",
                "netmask": "255.255.255.0",
                "gateway": "192.168.43.254"
            }
        }
    }
}

```

### DHCP Network Address

To set the controller to use DHCP network settings, the `eth0` setting must be set as in the example below.

```json
{
    "boot": {
        "network": {
            "eth0": null
        }
    }
}

```

Or just ommiting the `eth0` setting will also switch the controller to DHCP

```json
{
    "boot": {
        "network": {
        }
    }
}

```

### Optional Settings

The following settings can be set regardless of DHCP/Static addressing. If a setting is omitted or set to `null`, it's value will not be changed. To clear a setting, set the value to an empty string `""`.  For `ntpServer` setting the value to an empt string resets the value to it's default `pool.ntp.org`

```json
{
    "boot": {
        "network": {
            "primaryDns": "192.168.43.1",
            "secondaryDns": "",
            "ntpServer": "3.uk.pool.ntp.org",
            "sshEnabled": true
        }
    }
}
```

### Optional Settings: Logging

The fallowing settings allow forwarding log messages to the Custom Exchange platform or generic Azure Event Hub.

- [bool] **Enabled** - The logging system will start at controller boot.
- [bool] **DebugMessages** - Sets if debug messages should be sent. Turning debug messages off **greatly** reduces the number of messages forwarded.
- [string] - **Url** - connection string, for custom exchange this will be provided, for generic event hub targets, the url is of the form:

"amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/syslogmessages".  

The value for this can be found in the _Connection string–primary key_ under the shared access policy on the syslog event hub, the values map as follows

**{SAS Key Name}** = Endpoint=sb://loggingtarget.servicebus.windows.net/;SharedAccessKeyName=**deviceTransmitterKey**;SharedAccessKey=hdahduhcuihuhh38294uehduhhjkcasjad=

**{SAS key}** = Endpoint=sb://loggingtarget.servicebus.windows.net/;SharedAccessKeyName=deviceTransmitterKey;SharedAccessKey=**hdahduhcuihuhh38294uehduhhjkcasjad=**

**{namespace name}** = Endpoint=sb://**loggingtarget**.servicebus.windows.net/;SharedAccessKeyName=deviceTransmitterKey;SharedAccessKey=hdahduhcuihuhh38294uehduhhjkcasjad=

```json
{
    "boot": {
        "logging": {
            "enabled": true,
            "debugMessages": false,
            "url": "amqps://{SAS Key Name}:{SAS key}@{Namespace Name}.servicebus.windows.net/{SAS Hub Name}",
        }
    }
}


```
