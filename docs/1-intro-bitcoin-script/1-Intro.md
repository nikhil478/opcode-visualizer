FORTH is the parent of Bitcoin Script, which adds some grammar changes and removes capabilities such as jump instructions and loops.

In transactions on the Bitcoin ledger, each output contains a predicate called a lockScript (scriptPubKey). This predicate is written in Bitcoin script and evaluates an input against a set of conditions. When a transaction output is spent, the user provides a set of input data called the unlockScript (scriptSig) which is loaded onto the stack before the lockScript processes it. If the evaluation finishes with a single non-zero value on the stack, the unlockScript is valid and the tokens contained in the output can be spent in the transaction. For a transaction to be valid, every input must have a valid unlockScript and every new lockScript must meet the Bitcoin grammar

Bitcoin script predicates can only contain opcodes defined within the Bitcoin protocol itself. The evaluation ends when any of the following conditions are met:

The script reaches an OP_RETURN opcode
The script fails an OP_VERIFY check
The scriptSig is incomplete or invalid
The end of the script is reached
The script exceeds a policy such as the maximum stack memory policy

Note:
Bitcoin script cannot jump back to a previous instruction. For an input to a transaction to be valid its script must process without halting until it ends.