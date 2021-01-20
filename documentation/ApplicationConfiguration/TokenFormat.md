# Token Formats

Token formats define the decode rules for the binary data received from a card
reader and how to convert that into valid token data. The token formats block
allows you to define a token format once in the file and then reference it in
multiple reader definitions.

````json
{
    "controller": {
        "tokenFormats": [{
            "id": "28bit-Custom",
            "definition": "PHJlYWRlclRva2VuRG.....lRva2VuRGVmaW5pdGlvbj4="
            }
        ]
    }
}
````

## Properties

### Id

The name specified here is used to reference this reader format in the [Reader](Reader.md) tokenFormatType property.

### definition

This is a base64 encoded version of a reader definition. The reader definition
can be created and exported by the reader format tool (included in the SDK)
Details of the reader format can be found [here](TokenFormatDefinition.md)

## Tools

There is a Windows application that can be used to create and maintain token format definitions. It will also produce the definition in the base64 encoded format. Please ask Grosvenor Technology for details.
