# Request Authorization

## Authorization Header

All requests must contain the Authorization header with the following format.

`Authorization: amx {deviceSerial}:{requestSignature}:{nonce}:{timestamp}`

DeviceSerial is the unique identifier for the device. Signature is a Hash-based
Message Authentication Code (HMAC) that is constructed from the request and then
computed by using the SHA256 algorithm. Then, you encode it by using Base64
encoding.

Use this format to encode the SharedKey signature string:

````c#
StringToSign = {VERB}{Content-Length}{Content-Type}{nonce}{timestamp}{AbsoluteUri};

//The timestamp is the unix time, e.g.

var epochStart = new DateTime(1970, 01, 01, 0, 0, 0, 0, DateTimeKind.Utc);
var timeSpan = DateTime.UtcNow - epochStart;
var timestamp = Convert.ToUInt64(timeSpan.TotalSeconds).ToString();

//The nonce is a random 32 character string that should be unique to each request

var nonce = Guid.NewGuid().ToString("N");
````

An example of a signature string:

`POST123application/json;
charset=utf-8451f1b2ffe2c474687853abb013dce471512686109https://myserver.grovenor-oem.net/grosvenor-oem/device/123456/heartbeat`

When you have the signature string, encode it by using the HMAC-SHA256 algorithm
on the UTF-8-encoded string, and then encode the result as Base64. Use this
format:

`Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))`

The resulting encoded string should be added as a custom authorisation header
using the following format:

`Authorization: amx {DeviceSerial}:{Signature}:{nonce}:{timestamp}`

For example:

`Authorization: amx
TEST-SIW\~00009998:0Q9TsdP6P22r7bLAzmDWrYCaX9XKm8h2x52MrilyVww=:451f1b2ffe2c474687853abb013dce47:1512686109`
