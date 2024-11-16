Stack Operations

Stack operations include opcodes who's primary purpose is to process modifications to the stack, without creating any new or modified values.


Stack Duplicators

Stack duplicators are opcodes that duplicate items at one or more locations on the stack. The Duplicators are as follows:

Word	Input	Output	Description
OP_DUP	x	x x	Duplicates the top stack item.
OP_2DUP	x1 x2	x1 x2 x1 x2	Duplicates the top two stack items.
OP_3DUP	x1 x2 x3	x1 x2 x3 x1 x2 x3	Duplicates the top three stack items.
OP_IFDUP	x	x / x x	If the top stack value is not 0, duplicate it.
OP_OVER	x1 x2	x1 x2 x1	Copies the second-to-top stack item to the top.
OP_2OVER	x1 x2 x3 x4	x1 x2 x3 x4 x1 x2	Copies the pair of items two spaces back in the stack to the front.
OP_PICK	xn ... x2 x1 x0 <n>	xn ... x2 x1 x0 xn	The item n back in the stack is copied to the top.
OP_TUCK	x1 x2	x2 x1 x2	The item at the top of the stack is copied and inserted before the second-to-top item.
 

Stack Eliminators

Stack eliminators are opcodes that eliminate one or more items from the stack. The eliminators are as follows:

Word	Input	Output	Description
OP_DROP	x	Nothing	Removes the top stack item.
OP_2DROP	x1 x2	Nothing	Removes the top two stack items.
OP_NIP	x1 x2	x2	Removes the second-to-top stack item.
 

Stack Relocators

Stack relocators are opcodes that move one or more items from their locations on the stack to a new location on the stack. The relocators are as follows:

Word	Input	Output	Description
OP_SWAP	x1 x2	x2 x1	The top two items on the stack are swapped.
OP_2SWAP	x1 x2 x3 x4	x3 x4 x1 x2	Swaps the top two pairs of items.
OP_ROT	x1 x2 x3	x2 x3 x1	The top three items on the stack are rotated to the left.
OP_2ROT	x1 x2 x3 x4 x5 x6	x3 x4 x5 x6 x1 x2	The fifth and sixth items back are moved to the top of the stack.
OP_ROLL	xn ... x2 x1 x0 <n>	... x2 x1 x0 xn	The item n back in the stack is moved to the top.
 

Alt Stack Operators

The Bitcoin Script evaluation engine has a secondary 'Altstack' available to store data as needed for calculations. There are just 2 opcodes used to interface with the altstack, one which pushes data items onto the top and one which pulls data items from the top. The Altstack operates as a LIFO (Last In First Out) buffer.

The altstack is useful for storing items for later processing that would otherwise have to be moved to the back of the stack and retrieved.

Word	Input	Output	Description
OP_TOALTSTACK	x1	(alt)x1	Puts the input onto the top of the alt stack. Removes it from the main stack.
OP_FROMALTSTACK	(alt)x1	x1	Puts the input onto the top of the main stack. Removes it from the alt stack.
 

Example 1:

OP_DUP OP_1 OP_EQUAL OP_IF

    OP_DROP <pubkey1> OP_CHECKSIG

OP_ELSE

    OP_2 OP_EQUAL OP_IF

        <pubkey2> OP_CHECKSIG

    OP_ELSE

        OP_FALSE OP_RETURN

    OP_ENDIF

OP_ENDIF

In this example, one of two parties can sign a transaction, and indicate which party is signing the script by appending a 1 or a 2 to their solution. This can be spent using one of the two following unlockScripts:

<signature1> <1>

<signature2> <2>

If any other value other than 1 or 2 is at the top of the stack, the script will fail.

This can be useful as it forces the signing party to indicate which key is being used within the script, allowing their identity to be captured without evaluating signatures.

Example 2:

OP_2DROP <pubkey> OP_CHECKSIG

In this example, the output being spent is used to capture data on the ledger which is not relevant to the spending of the token. This script can be validly spent using the following unlockScript:

<signature> <data_item1> <data_item2>

Example 3:

OP_IFDUP OP_IF

    <pubkey1> <pubkey2> 2 OP_CHECKMULTISIG

OP_ELSE

   <pubkey> OP_CHECKSIG

OP_ENDIF

In this example, the first loop can be entered if either 1, 2, or 3 of the signing parties required choose to sign using one of the following unlockScripts:

<x> <signature1> <1>

<x> <signature2> <1>

<x> <signature1> <signature2> <2>

In these solutions, the number of signatures to be checked is the last item on the stack, confirming the number of signatures needed.

To enter the second loop, the signing party would use the following unlockScript:

<signature> <0>

The 0 tells the script that the second entity wishes to sign using a single signature check, so there is no need to keep the value on the stack.

Example 4:

OP_OVER OP_SIZE OP_1SUB OP_SPLIT OP_NIP <sighash> OP_EQUAL OP_NOTIF

    OP_FALSE OP_RETURN

OP_ENDIF

In this example OP_OVER is used to bring the signature to the top of the stack. It's size is queried and subtracted by 1 before it is split, and the main part nipped from the stack, leaving just the SIGHASH flag which is then checked against a particular mask. If the correct SIGHASH flag is not used, the script fails. Opcodes such as OP_SIZE, OP_EQUAL and OP_1SUB will be covered in later parts of this chapter. For further information on the structure of DER signatures in Bitcoin, please visit this page.

Example 5:

OP_1SUB OP_DUP OP_TOALTSTACK OP_NOTIF

    2 <pubkey1> <pubkey2> 2 OP_CHECKMULTISIG

    OP_FROMALTSTACK OP_DROP

OP_ELSE

    OP_FROMALTSTACK OP_1SUB OP_NOTIF

        <pubkey> OP_CHECKSIG

    OP_ELSE

        OP_FALSE OP_RETURN

    OP_ENDIF

OP_ENDIF

In this example, the spending party is one of two entities. To spend the transaction they must indicate to the script using an integer value of 1 or 2 which path they wish to take. The first entity requires a 2of2 multisignature solution which they would solve with the following unlockScript:

<x> <signature1> <signature2> <1>

with the first element <x> being required due to a protocol bug (explained later), then the 2 signatures and the integer value <1> to show they wish to enter the first loop of the transaction.

By subtracting 1 from the value before duplicating it and storing the copy on the altstack, we can save space by performing a NOTIF check rather than comparing the value using OP_1 OP_EQUAL OP_IF. The value stored on the altstack is then pulled and dropped.

If the second entity wishes to sign, they would use the following unlockScript:

<signature> <2>

After the loop identifier is pulled from the altstack, a second subtraction is performed before a NOTIF check is done. In the second loop a single signature check is performed before the nested IF loops are exited.

If the top value on the stack is neither 1 nor 2, the script will enter the nested OP_ELSE statement where OP_FALSE OP_RETURN will cause it to fail.