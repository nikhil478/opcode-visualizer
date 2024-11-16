Constant Value and PUSHDATA Opcodes

There are two types of opcodes that can add data to the main stack in Bitcoin script:

Single byte opcodes that place a constant on top of the stack
Pushdata opcodes that allow data of any length up to 4.3GB to be placed on top of the stack
Opcodes for pushing constants onto the stack

Each of these Opcodes allows you to use a single programmatical step cause a particular numeric value to be left on the stack. There are 18 'Constant value' opcodes available in the Bitcoin scripting language as follows:

Word	Input	Output	Description
OP_0, OP_FALSE	Nothing.	Null item	An empty array of bytes is pushed onto the stack.
OP_1NEGATE	Nothing.	-1	The number -1 (0x81) is pushed onto the stack.
OP_1, OP_TRUE	Nothing.	1	The number 1 (0x01) is pushed onto the stack.
OP_2-OP_16	Nothing.	2-16	A 1-byte integer of the value in the word name (0x02 - 0x0F) is pushed onto the stack.
Opcodes like these can be used to feed inputs to other functions, making it simple and easy to configure operations such as multi-signature checks for groups of 16 people or less.


Example:

OP_2 <pubkey_1> <pubkey_2> <pubkey_3> OP_3 OP_CHECKMULTISIG

This example script is a basic 2of3 multisignature script, and uses OP_2 and OP_3 to push integer values '2' and '3' onto the stack to instruct the validation engine as to how many valid signatures are required (2) and how many public keys are in the keypool (3).

Pushdata Opcodes

Pushdata opcodes are opcodes that push data items of a particular length onto the stack. Using these opcodes it is possible to push data items from 1 byte up to 4.3 gigabytes onto the stack.

Unlike other opcodes, these opcodes do not use any items from the stack, but rather use the following data from the script as instructions for what to push to the stack.

Word	Input	Output	Description
Pushdata Bytelength in HEX	none	data	The next <opcode> bytes is data to be pushed onto the stack. Used for data items up to 75 bytes in length.
OP_PUSHDATA1	none	data	The next byte contains the number of bytes to be pushed onto the stack. Used for data items from 76 bytes to 255 bytes in length.
OP_PUSHDATA2	(special)	data	The next two bytes contain the integer number of bytes to be pushed onto the stack in little endian format. Used for data items from 256 bytes to 65,535 bytes in length.
OP_PUSHDATA4	(special)	data	The next four bytes contain the integer number of bytes to be pushed onto the stack in little endian format. Used for data items from 65,536 bytes to 4,294,967,295 bytes in length.
 

Pushdata for items up to 75 bytes

For data items that are 75 bytes or less, the scripting language has separate opcodes that allow those items to be pushed onto the stack. These opcodes are useful for typical data items used in Bitcoin script which include public keys and ECDSA signatures in Bitcoin format.

Example:

0x48 <signature> 0x20 <public_key> OP_CODESEPARATOR OP_DUP OP_HASH160 0x14 <public_key_hash> OP_EQUALVERIFY OP_CHECKSIG

This example is a fully assembled 'Pay to Public Key Hash' script as it would be evaluated by a node on the network. In this example, everything after OP_CODESEPARATOR is retrieved from the UTXO being spent, and everything before OP_CODESEPARATOR is supplied by the spending party as the unlockScript. OP_CODESEPARATOR is inserted by the script evaluation engine to mark the separation between unlockScript and lockScript.

There are three examples of pushdata opcodes in the script.

The first uses the 0x48 opcode to push a 72 byte signature onto the stack.
The second uses the 0x20 opcode to push a 32 byte public key onto the stack.
The third uses 0x14 to push the expected 20 byte public key hash onto the stack.
We will explore Pay to Public Key Hash (P2PKH) scripts in more detail in chapter 3.

Larger pushdata items

For larger data items, a set of three pushdata opcodes, OP_PUSHDATA1, OP_PUSHDATA2 and OP_PUSHDATA4 are available. These opcodes each expect a pair of data items to be present in the subsequent script. The first is a length indicator containing the length of the data item being added to the script. The size of this data item is dependent on the opcode used, with OP_PUSHDATA1 expecting a 1 byte length indicator used to push up to 255 bytes, OP_PUSHDATA2 expecting a 2 byte indicator used to push up to 65535 bytes and OP_PUSHDATA4 expecting a 4 byte indicator, supporting data items up to 4,294,967,296 bytes.

Example 1:

OP_PUSHDATA1 0x64 <100_byte_data_item>

This example first uses OP_PUSHDATA1 followed by 0x64 to push a 100 byte data item to the stack

Example 2:

OP_PUSHDATA2 0xe803 <1kB_data_item>

In this example OP_PUSHDATA2 is used followed by 0xe803 which is the little endian hexadecimal value for 1000, indicating that a 1kB data item is being pushed onto the stack.

Example 3:

OP_PUSHDATA4 0x40420f00 <1MB data item>

In this example OP_PUSHDATA4 is used followed by 0x40420f00 which is the little endian hexadecimal value for 1,000,000, indicating that a 1MB data item is being pushed onto the stack.

Minimal Encoding Rule

One rule that is asserted by nodes on the Bitcoin network is that any pushdata operands must be 'minimally encoded'. This means that if a pushdata item of a particular length is being added to the stack, only the correct pushdata opcode can be used.

For example, to push a 100 byte data item to the stack, the following applies:

OP_PUSHDATA1 0x64 <100B data item> is valid Bitcoin script but OP_PUSHDATA2 0x6400 <100B data item> violates the minimal encoding rules and any transaction containing this script element in an output will be rejected.

Pushdata Opcode Notation In Script

It is important to note that most Bitcoin script interpreters or programming tools will insert the correct pushdata opcode for the data item being pushed, respecting the minimal encoding rule. Typically the user will need only to provide the data item itself.

Examples shown in subsequent pages/chapters will exclude pushdata opcodes from the script to simplify the expressions and allow you to focus on the opcodes being discuss

Input Script Opcode Rule

Another rule asserted by nodes on the Bitcoin network is that only constant data and pushdata opcodes can be used to form valid input scripts. Any transaction submitted to the network that uses opcodes other than constant data/pushdata opcodes is considered invalid and will be rejected.