# Token Format Definition

## Data Format Definitions

A token data format has three parts:

- Physical interface
- Data validation
- Data interpretation

A reader often has to support more than one data format. All data formats for
particular reader have to have the same physical interface type. Each data
format, for that reader, is tried in turn. A data format is accepted when it
passes the data format's validation.

### Physical Interface

This defines how the data is transmitted from the reader to the Blade of an
Advance Controller. The following will be supported:

- **Wiegand** - Uses two data lines, often referred to as Data0 and Data1.
- **Clock and Data** - Uses two lines, one a clock and the other data.

### Data validation

There are different validation and interpretation methods dependant on the
format of the data. It normally follows that a Wiegand physical interface uses a
Wiegand data format and a Clock and Data physical interface uses a Mag-Stripe
data format. This is not always the case though.

#### Wiegand validation

The following can be checked:

- Both data lines are not asserted at the same time – Checked by the Blade firmware.
- The number of data bits matches the data format.
- The data format can have one or more parity bit ranges.
- It has one or more ‘constant’ bit sequences.

#### Mag-stripe (ISO 7811/2 Track 2) validation

The following can be checked:

- Conforms to ISO standard – correct parity and longitudinal redundancy check (LRC)
- Correct number of characters
- It has one or more ‘constant’ character sequences.

### Data interpretation

#### Wiegand interpretation

Several sequences of bits within the captured data can make up the number. The
number can then be submitted as a token number or keypad digit/control code.

#### Mag-stripe (ISO 7811/2 Track 2) interpretation

Several sequences of numerical characters within the captured data can make up
the number. The number can then be submitted as a token number or keypad
digit/control code.

## XML Document Definition

Because of some fundamental differences between Wiegand and Mag-Stripe formats
they are dealt with separately below. To confuse things, it is possible for
Wiegand data to use Clock & Data lines and Mag-Stripe data to use Wiegand lines.
This is rare, but is catered for.

After using the document to decode a read, the controller will have a list of
read results. These read results are key value pairs consisting of the data
names and values. The current data names supported by the Advance Controller
access control application are:

- **tokenNumber** - Used to give token number
- **keypadNumber** - Used to give all keypad digits as a number to be used as a PIN
- **keypadDigit** - Used to give keypad digit
- **keypadClear** - Used to clear keypad digit buffer
- **keypadBackSpace** - Used to delete previous keypad digit
- **keypadEnter** - Used to signify end of keypad digit input (PIN input does not require this as they are of fixed length)
- **keypadAndTokenEnter** - Used to signify end of keypad digit input, will allow a token number to be entered, regardless of reader settings.
- **lowBattery** - Used to give indication when a token has a low battery. '1' indicates low battery, '0' battery OK.
- **deniedByReader** - Used to indicate that the reader denies permission. Usually because of biometric verification failure. '1' indicates denied, '0' is OK.
- **readerTamper** - Used to indicate reader tamper. '1' indicates in tamper, '0' is OK. Normally sent in it's own format definition, not in a 'normal' read.

In addition to the above there are the following reserved data names which are
used by the decoder to convey additional information:

- **readFormat** - Gives the name of format found
- **decodeError** - Gives an error message
- **diags** - Gives read data bits

### Wiegand Document Definition

An example of a Wiegand document:

````xml
<?xml version="1.0" encoding="utf-8"?>
<readerTokenDefinition name="An example Wiegand decode" readBothWays="true" readerType="wiegand">

  <format name="CASI 28 bit" numberOfBits="28">
    <parityChecks>
      <parityCheck parity="even" firstBitIndex="1" lastBitIndex="14" />
      <parityCheck parity="odd" firstBitIndex="15" lastBitIndex="28" />
      <parityCheck parity="even" firstBitIndex="2" lastBitIndex="26" parityBits="P.PP.PP.PP.PP.PP.PP.PP.PP" />
      <parityCheck parity="even" firstBitIndex="3" lastBitIndex="27" parityBits="P.PP.PP.PP.PP.PP.PP.PP.PP" />
    </parityChecks>
    <interpretations>
      <interpretation dataName="tokenNumber">
        <bitSequences>
          <bitSequence lsbIndex="27" numberOfBits="15" reversed="false" multiplier="1" />
          <bitSequence lsbIndex="12" numberOfBits="8" reversed="false" multiplier="100000" />
        </bitSequences>
      </interpretation>
    </interpretations>
  </format>

  <format name="Indala 34 bit" numberOfBits="34">
    <constantChecks>
      <constantCheck firstBitIndex="28" constantBits="0110111" />
    </constantChecks>
    <interpretations>
      <interpretation dataName="tokenNumber">
        <bitSequences>
          <bitSequence lsbIndex="27" numberOfBits="14" reversed="false" multiplier="1" />
          <bitSequence lsbIndex="13" numberOfBits="13" reversed="false" multiplier="100000" />
        </bitSequences>
      </interpretation>
    </interpretations>
  </format>

  <format name="Mifare Forward" numberOfBits="32">
    <interpretations>
      <interpretation dataName="tokenNumber">
        <bitSequences>
          <bitSequence lsbIndex="32" numberOfBits="32" reversed="false" multiplier="1" />
        </bitSequences>
      </interpretation>
    </interpretations>
  </format>

  <format name="Keypad" numberOfBits="4">
    <interpretations>
      <interpretation dataName="keypadDigit">
        <bitSequences>
          <bitSequence lsbIndex="4" numberOfBits="4" reversed="false" multiplier="1" />
        </bitSequences>
        <controlCodes>
          <controlCode codeNumber="10" dataName="keypadClear" />
          <controlCode codeNumber="11" dataName="keypadEnter" />
        </controlCodes>
      </interpretation>
    </interpretations>
  </format>

</readerTokenDefinition>
````

The document falls into these parts:

#### Document Root

The `readerTokenDefinition` element is the container for the individual formats
to be tried. It has the following attributes:

| Attribute name     | Required | Type                 | Comment                                              |
|--------------------|----------|----------------------|------------------------------------------------------|
| name               | true     | string               | Describes definition.                                |
| readBothWays       | true     | boolean              | Attempt to read data backwards if not read forwards. |
| readerType         | true     | enumerable           | Set to `wiegand`.                                    |
| invertData         | false    | boolean              | Defaults to false.                                   |
| Child Element name | Required | Comment              |                                                      |
| format             | true     | One or more formats. |                                                      |

#### Format

One or more `format` child elements are required within the
`readerTokenDefinition`. If the number of data bits in the read does not match
the `numberOfBits` attribute, the attempt to read using this format will be
aborted.

| Attribute name     | Required | Type                                             | Comment                                                                         |
|--------------------|----------|--------------------------------------------------|---------------------------------------------------------------------------------|
| name               | true     | string                                           | Describes format.                                                               |
| numberOfBits       | true     | integer                                          | Number of bits this format has.                                                 |
| diags              | false    | boolean                                          | Turn on extra reporting.                                                        |
| formatType         | false    | string                                           | Can over-ride default for reader type, if using clock and data set to `wiegand` |
| Child Element name | Required | Comment                                          |                                                                                 |
| constantChecks     | false    | Contains a list of zero or more constant checks. |                                                                                 |
| parityChecks       | false    | Contains a list of zero or more parity checks.   |                                                                                 |
| interpretations    | true     | Contains a list of one or more interpretations.  |                                                                                 |

#### Constant Check

The `constantCheck` element is used to define data that must be found at a
particular location within the read. If not found the attempt to read using this
format will be aborted. It is sometimes used to determine which way to read the
data.

| Attribute name | Required | Type    | Comment                                |
|----------------|----------|---------|----------------------------------------|
| firstBitIndex  | true     | integer | Data bit numbering starts at one.      |
| constantBits   | true     | binary  | A string of one or more binary digits. |

#### Parity Check

The `parityCheck` element is used to define data bits within the read that must
have a particular parity. If not correct the attempt to read using this format
will be aborted. It is sometimes used to determine which way to read the data.

| Attribute name | Required | Type       | Comment                                                                                                                                      |
|----------------|----------|------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| parity         | true     | enumerable | Either `odd` or `even`.                                                                                                                      |
| firstBitIndex  | true     | integer    | Data bit numbering starts at one.                                                                                                            |
| lastBitIndex   | true     | integer    | Data bit numbering starts at one.                                                                                                            |
| parityBits     | false    | string     | A string of two or more ‘P’ or ‘.’ characters. Used to pick particular bits with the defined range to be included in the parity calculation. |

#### Interpretation

The `interpretation` element is used to get data from a read. The raw data can
be in more than one bit sequences and manipulated to get the data into the
format required. Only one interpretation per format is currently required,
though battery and tamper data may be extracted in the future by using other
interpretations.

| Attribute name     | Required | Type                                           | Comment                                                                         |
|--------------------|----------|------------------------------------------------|---------------------------------------------------------------------------------|
| dataName           | true     | enumerable                                     | Often one of the following: `tokenNumber`, `keypadNumber` or `keypadDigit`. See above for full list.|
| Child Element name | Required | Comment                                        |                                                                                 |
| bitSequences       | true     | Contains a list of one or more bit sequences.  |                                                                                 |
| controlCodes       | false    | Contains a list of zero or more control codes. |                                                                                 |

#### Bit Sequence

The `bitSequence` element gets a number from a bit sequence. If there are more
than one bit sequences the resulting numbers are added together. It has the
following attributes:

| Attribute name | Required | Type    | Comment                                                                                                                                                           |
|----------------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| lsbIndex       | true     | integer | Least Significant Bit index. Data bit numbering starts at one.                                                                                                    |
| numberOfBits   | true     | integer | Number of data bits in sequence                                                                                                                                   |
| reversed       | true     | boolean | Normally the Least Significant Bit index is higher than the Most Significant Bit index. If this is not the case, `reversed` is true.                              |
| bcd            | false    | boolean | True if bits are BCD (Binary Coded Decimal) encoded. Number of bits must be a multiple of 4. Default is false.                                                    |
| multiplier     | true     | integer | The resulting number from a bit sequence is multiplied by the multiplier (use a multiplier of 1 to leave alone). Used to make a site code prefix the main number. |

#### Control Code

The `controlCode` element is used to override the interpretations `dataName`
with another. An example of this functionality would be to change a keypad digit
into a keypad control code like clear, backspace and enter.

| Attribute name | Required | Type       | Comment                                                                                                   |
|----------------|----------|------------|-----------------------------------------------------------------------------------------------------------|
| codeNumber     | true     | integer    | Number to match with number obtained from the bit sequences.                                              |
| dataName       | true     | enumerable | Currently one of the following: `keypadClear`, `keypadBackSpace`, `keypadEnter` or `keypadAndTokenEnter`. |

### Mag-stripe Document Definition

An example of a Mag-stripe document:

````xml
<?xml version="1.0" encoding="utf-8"?>
<readerTokenDefinition name="MagStripe" readBothWays="true" readerType="magstripe">
  <format name="Grosvenor Magstripe" numberOfChars="10">
    <constantChecks>
      <constantCheck firstCharIndex="1">
        <constantChars>CE</constantChars>
      </constantCheck>
    </constantChecks>
    <interpretations>
      <interpretation dataName="tokenNumber">
        <charSequences>
          <charSequence lsdIndex="10" numberOfChars="8" reversed="false" multiplier="1" />
        </charSequences>
      </interpretation>
    </interpretations>
  </format>
  <format name="Other Magstripe" numberOfChars="any" diags="true">
    <constantChecks />
    <interpretations>
      <interpretation dataName="tokenNumber">
        <charSequences>
          <charSequence numberOfChars="all" reversed="false" multiplier="1" />
        </charSequences>
      </interpretation>
    </interpretations>
  </format>
</readerTokenDefinition>
````

The document falls into these parts:

#### Document Root

The `readerTokenDefinition` element is the container for the individual formats
to be tried. It has the following attributes:

| Attribute name     | Required | Type                 | Comment                                              |
|--------------------|----------|----------------------|------------------------------------------------------|
| name               | true     | string               | Describes definition.                                |
| readBothWays       | true     | boolean              | Attempt to read data backwards if not read forwards. |
| readerType         | true     | enumerable           | Set to `magstripe`.                                  |
| invertData         | false    | boolean              | Defaults to false.                                   |
| Child Element name | Required | Comment              |                                                      |
| format             | true     | One or more formats. |                                                      |

#### Format

One or more `format` child elements are required within the
`readerTokenDefinition`. If the number of data characters in the read does not
match the `numberOfChars` attribute, the attempt to read using this format will
be aborted. The start sentinel, end sentinel and LRC are not counted.

| Attribute name     | Required | Type                                             | Comment                                                                 |
|--------------------|----------|--------------------------------------------------|-------------------------------------------------------------------------|
| name               | true     | string                                           | Describes format.                                                       |
| numberOfChars      | true     | integer or `any`                                 | Number of characters this format has.                                   |
| diags              | false    | boolean                                          | Turn on extra reporting.                                                |
| formatType         | false    | string                                           | Can over-ride default for reader type, if using Wiegand set to `t2iso`. |
| Child Element name | Required | Comment                                          |                                                                         |
| constantChecks     | false    | Contains a list of zero or more constant checks. |                                                                         |
| interpretations    | true     | Contains a list of one or more interpretations.  |                                                                         |

#### Constant Check

The `constantCheck` element is used to define data that must be found at a
particular location within the read. If not found the attempt to read using this
format will be aborted.

| Attribute name | Required | Type        | Comment                                     |
|----------------|----------|-------------|---------------------------------------------|
| firstCharIndex | true     | integer     | Character numbering starts at one.          |
| constantChars  | true     | hexadecimal | A string of one or more hexadecimal digits. |

#### Interpretation

The `interpretation` element is used to get data from a read. The raw data can
be in more than one character sequences and manipulated to get the data into the
format required. Only one interpretation per format is currently required,
though battery and tamper data may be extracted in the future by using other
interpretations.

| Attribute name     | Required | Type                                                | Comment                                                                         |
|--------------------|----------|-----------------------------------------------------|---------------------------------------------------------------------------------|
| dataName           | true     | enumerable                                          | Often one of the following: `tokenNumber`, `keypadNumber` or `keypadDigit`. See above for full list. |
| Child Element name | Required | Comment                                             |                                                                                 |
| charSequences      | true     | Contains a list of one or more character sequences. |                                                                                 |
| controlCodes       | false    | Contains a list of zero or more control codes.      |                                                                                 |

#### Character Sequence

The `charSequence` element gets a number from a character sequence. If there are
more than one character sequences the resulting numbers are added together. It
has the following attributes:

| Attribute name | Required | Type             | Comment                                                                                                                                                                 |
|----------------|----------|------------------|-------------------------------------------------------------------|
| lsdIndex       | true     | integer          | Least Significant Digit index. Character numbering starts at one. |
| numberOfChars  | true     | integer or `all` | Number of characters in sequence                                  |
| reversed       | true     | boolean          | Normally the Least Significant Digit index is higher than the Most Significant Digit index. If this is not the case, `reversed` is true. |
| multiplier     | true     | integer          | The resulting number from a character sequence is multiplied by the multiplier (use a multiplier of 1 to leave alone). Used to make a site code prefix the main number. |

N.B. If the number of characters in a character sequence is set to 1, then that
character is interpreted as a number between 0x00 and 0x0E (not 0x0F as this is
used as the end sentinel). This allows a mag-stripe control code to be used as a
keypad control code by defining them as such. Otherwise mag-stripe control codes
are skipped and do not affect the number returned.

#### Control Code

The `controlCode` element is used to override the interpretations `dataName`
with another. An example of this functionality would be to change a keypad digit
into a keypad control code like clear, backspace and enter.

| Attribute name | Required | Type       | Comment                                                                                                   |
|----------------|----------|------------|-----------------------------------------------------------------------------------------------------------|
| codeNumber     | true     | integer    | Number to match with number obtained from the character sequences.                                        |
| dataName       | true     | enumerable | Currently one of the following: `keypadClear`, `keypadBackSpace`, `keypadEnter` or `keypadAndTokenEnter`. |
