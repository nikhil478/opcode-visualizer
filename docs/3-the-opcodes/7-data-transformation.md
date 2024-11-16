Data transformation

Data transformation opcodes are used to change the data on the stack, either by splitting one item into multiple items, joining items together, or changing an item to a forms required by the script processing engine.

Splitting and joining data items

When handling data items on the stack, they may need to be split or joined as part of the script functionality. There are two opcodes for these functions as defined below:

Word	Input	Output	Description
OP_CAT	x1 x2	out	Concatenates two strings.
OP_SPLIT	x n	x1 x2	Splits byte sequence x at position n.
 

Example:

0x12345678 OP_1 OP_SPLIT

This example splits the bytevector 0x12345678 into 0x12 and 0x345678

Example:

0x12345678 OP_1 OP_SPLIT OP_1 OP_SPLIT OP_1 OP_SPLIT OP_SWAP OP_CAT OP_SWAP OP_CAT OP_SWAP OP_CAT

This example snippet changes the endianness of the 4 byte integer 0x12345678, leaving 0x78563412 on the stack. This is useful when transaction inputs are submitted in big-endian format but need to be changed to little endian to undergo mathematical transformation.

Data Type Transformations

These two opcodes are not part of the original Bitcoin script definition. They were used when Bitcoin script limited mathematical functions to data items no larger than 4 bytes in size. OP_NUM2BIN can be used when string items need to be extended to arbitrary lengths with leading zeroes. OP_BIN2NUM transforms a string to a minimally encoded number by stripping any leading zeroes.

Word	Input	Output	Description
OP_NUM2BIN	a b	out	Converts numeric value a into byte sequence of length b.
OP_BIN2NUM	x	out	Converts byte sequence x into a minimally encoded numeric value.
 

Example 1:

OP_16 OP_NUM2BIN

In this example, a string of uncertain length is extended to 16 bytes.

e.g. 0x010203040506070809 -> 0x010203040506070809000000000000

Example 2:

OP_BIN2NUM

In this example, a string of uncertain length is cut back to its minimal integer representation

e.g. 0x010203040506070809000000000000 -> 0x010203040506070809