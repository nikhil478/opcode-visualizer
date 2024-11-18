Bitcoin's Transaction Protocol

All transactions are defined using a serialisation format comprised of a number of fixed and variable length fields concatenated into a single string. This can be likened to a Protobuffer.

Transaction are created using the following elements:

Version No. (4 bytes)
Quantity of transaction inputs (varInt, 1-9 bytes) ← link to Varint page on BSV wiki
The list of inputs in input structure format which is defined as:
The TXID the input is from (32 byte little endian hash)
The output index of the input (4 byte little endian integer)
The length of the unlockScript (varInt, 1-9 bytes)
The unlockScript
The input's nSequence value (4 byte little endian integer)
Quantity of transaction outputs (varInt, 1-9 bytes)
The list of outputs in output structure format which is defined as:
The output's satoshi value (8 byte little endian integer)
The length of the lockScript
The lockScript
The transaction's nLocktime
The transaction itself is all of these elements serialised as a bytevector.

This is the public record of Satoshi Nakamoto sending Hal Finney 10 Bitcoins in block no. 170.:

0100000001c997a5e56e104102fa209c6a852dd90660a20b2d9c352423edce25857fcd3704000000004847304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901ffffffff0200ca9a3b00000000434104ae1a62fe09c5f51b13905f07f06b99a2f7159b2225f374cd378d71302fa28414e7aab37397f554a7df5f142c21c1b7303b8a0626f1baded5c72a704f7e6cd84cac00286bee0000000043410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac00000000