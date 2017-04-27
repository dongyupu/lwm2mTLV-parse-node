# lwm2mTLV-parse-node
Parse OMA-TS-LightweightM2M-V1_0  6.4.3 TLV descripted TLV format

For “Read” and “Write” operations, the binary TLV (Type-Length-Value) format is used to represent an array of values or a
singular value using a compact binary representation, which is easy to process on simple embedded devices. The format has a
minimum overhead per value of just 2 bytes and a maximum overhead of 5 bytes depending on the type of Identifier and
length of the value. The maximum size of an Object Instance or Resource in this format is 16.7 MB. The format is selfdescribing,
thus a parser can skip TLVs for which the Resource is not known.

This data format has a Media Type of application/vnd.oma.lwm2m+tlv.

The format is an array of the following byte sequence, where each array entry represents an Object Instance, Resource, or
Resource Instance:

| Field         | Format and Length | Description            |
| ------------- |:-----------------|:----------------------|
| Type          | 8-bits masked field:<br>0bxxxxxxxx (MSB is the bit following 0b)<br> Bit numbering is 0 for the LSB to 7 for the MSB |                                                     Bits 7-6: Indicates the type of Identifier.<br>00= Object Instance in which                                                             case the Value contains one or more Resource TLVs<br> 01= Resource Instance                                                             with Value for use within a multiple Resource TLV<br>10= multiple Resource,                                                             in which case the Value contains one or more Resource Instance TLVs <br>11=                                                             Resource with Value<br><br>Bit 5: Indicates the Length of the Identifier.                                                               <br>0=The Identifier field of this TLV is 8 bits long<br>1=The Identifier                                                               field of this TLV is 16 bits long<br><br>Bit 4-3: Indicates the type of                                                                 Length.<br>00=No length field, the value immediately follows the                                                                         Identifier field in is of the length indicated by Bits 2-0 of this                                                                       field<br>01 = The Length field is 8-bits and Bits 2-0 MUST be ignored<br>10                                                             = The Length field is 16-bits and Bits 2-0 MUST be ignored<br>11 = The                                                                   Length field is 24-bits and Bits 2-0 MUST be ignored<br><br>Bits 2-0: A 3-                                                               bit unsigned integer indicating the Length of the Value.              |
| Identifier      | 8-bit or 16-bit unsigned integer as indicated by the Type field. |   The Object Instance, Resource, or Resource                                                                                                 Instance ID as indicated by the Type field |
| Length | 0-24-bit unsigned integer as indicated by the Type field.                 |   The Length of the following field in bytes. |
| Value | Sequence of bytes of Length                  |   Value of the tag. The format of the value depends on the Resource’s data type                                                              (See Appendix C).                                 |



Table 21: TLV format and descriptio<br><br>
Each TLV entry starts with a Type byte that indicates if the TLV contains an Object Instance, a Resource, multiple Resources, or a Resource Instance. Object Instance and Resource with Resource Instance TLVs contains other TLVs in their value. The hierarchy is as follows and may be up to 3 levels deep.
<br>

      * Object Instance TLV, which contains 
          * Resource TLVs or
          * multiple Resource TLVs, which contains
              * Resource Instance TLVs

<br>For simplicity and to prevent any ambiguity on Object Instance Identity, when the Object Instance ID is not specified in the request, the Object Instance TLV MUST be used, whatever the number of Object Instances (1 or more) to return to the LwM2M Server.
When a Multiple Resource has to be returned to the LwM2M Server, the Multiple Resource TLV MUST be used whatever the number of Instances (0, 1 or more) of that resource.
<br>The following figure illustrates the possible nesting of Object Instance, Resource, multiple Resources, and Resource Instance TLVs. One or several Resource TLVs, and/or one or several multiple Resource TLVs MAY be nested in an Object Instance
TLV. A multiple Resource TLV contains one or several Resource Instance TLVs.
