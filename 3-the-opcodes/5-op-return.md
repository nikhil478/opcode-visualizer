History of OP_RETURN
The OP_RETURN opcode has seen historical changes to its functionality that have resulted in it being used as the primary mechanism to store arbitrary data on-chain.

Its original functionality was as a return operation that ends execution of the script prematurely, with the topmost stack item presented as the execution result.

The original implementation of the opcode created an easily exploitable vulnerability which was quickly patched by Satoshi Nakamoto.

1 RETURN Exploit
OP_RETURN was originally intended to terminate the script, presenting the top value on the stack as the result of the execution.

Given the script:

OP_TRUE OP_RETURN

True would be returned from the script and the script would terminate, ignoring any opcodes that came afterwards; because of this, this example script could prepend any unlocking script, granting the ability for one to steal anyone's Bitcoin.

The functionality of the opcode was changed to always return false instead of returning the value on top of the stack.

Further changes to OP_RETURN functionality
In Bitcoin Core's v0.9.0 update, the 'OP_RETURN output' script was made into a standard output type, allowing users to append data to unspendable transaction outputs. The amount of data usable in these scripts was first limited to 40 bytes, and then later increased to an 80 byte limit.

Storing data on-chain
The modification to OP_RETURN to always return false had interesting implications. Any opcodes or data are not evaluated after the OP_RETURN so users of the network began using this opcode to store arbitrary data.

This opcode was used instead of the OP_PUSHDATA opcodes since the max script size was limited to 520 bytes. These two fundamental changes to Bitcoin resulted in the opcode being used in an unforeseen and interesting way, outside of its original intent.

OP_RETURN in BCH
During the period while Bitcoin was more widely known as Bitcoin Cash (BCH), the length of data able to be appended to OP_RETURN outputs was extended to 220 bytes, allowing for the creation of larger data items and promoting its use in novel ways such as the creation of social media posting services and more.

OP_RETURN in BSV
For a short while after the network was transitioned to a majority of nodes running the BitcoinSV node client, the 220 byte limit was retained for a short period. Then, in January 2019, it was shown that since the OP_RETURN opcode terminated scripts in such a way that nodes did not validate any subsequent opcodes, that nodes also did not check that the script was within the max script size limit of 520 bytes. This was communicated to node operators on the network who were then able to raise the maximum transaction size to 100KB, giving users the freedom to create novel implementations that put larger and more complex data onto the ledger including in this early example, an entire website.

Return to original functionality
The Genesis upgrade in February 2020 restored the original functionality of the opcode. This did not impact the ability to store arbitrary data on-chain, however the best practice was amended to advise the use of False Return scripts for applications that still seek to do so.

OP_RETURN causes the script to terminate. If there is a single non-zero item on the stack the script will return TRUE, allowing it to be spent. If the topmost stack value is zero, or there are multiple items on the stack, the script fails and the transaction cannot be submitted to the blockchain.

This functionality can be applied to a multitude of use cases.


Example:

OP_DEPTH OP_1 OP_EQUAL OP_IF

    <public_key> OP_CHECKSIG

    OP_RETURN

OP_ENDIF

<rest_of_script>

In this example we first check the depth of the stack. This tells us how many items there are on the stack at this moment in the script processing. If there is just 1 item, the script expects that this will be a signature, that can be verified using the public key in the script. If the OP_CHECKSIG operation finds a valid signature, OP_RETURN will end the script successfully. If the signature check is invalid, the script will terminate in failure, and the output cannot be spent.

In this example, it is assumed that any further actions that take place in <rest_of_script> require two or more stack items in order to successfully validate.

The functionality of OP_CHECKSIG and other variations is covered in page 9 of this chapter.

FALSE RETURN scripts

One particular use-case for OP_RETURN is in the creation of FALSE RETURN scripts (commonly called 'OP_RETURN outputs').

A False Return script is created by generating a transaction output which uses OP_FALSE and OP_RETURN as the first two opcodes in its script. Because a script that begins with OP_FALSE OP_RETURN will always terminate in failure, these scripts are considered unspendable and are the only type of script that can be published with an output value of zero. As such they can be used as carriers for data items that do not form part of any locking conditions.

This technique is widely used on the BitcoinSV network for a variety of token solutions, and to allow platforms and applications to capture data onto the blockchain for indexing and analysis.

Example:

OP_FALSE OP_RETURN <data packet>

In this example, OP_FALSE is pushed onto the stack before the OP_RETURN opcode ends the script. Because OP_RETURN always returns with a false result this script can never be spent.