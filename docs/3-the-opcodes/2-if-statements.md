IF Statements

Flow control opcodes are opcodes that allow a script to either execute or ignore particular sections of code, or to terminate the script process depending on the value of the topmost stack items.

There are a variety of OPCODES available to trigger entry into IF statements including:

OP_IF, OP_NOTIF,


IF / NOTIF statements

IF statements are used to allow Bitcoin script to do comparative analysis on stack data items to control entry into IF statements. IF statejehts are the only means provided in Bitcoin script to perform different operations depending on previous processing.

The format of a simple IF statement is as follows:

<Expression>

OP_IF

    <True action>

OP_ENDIF


In this example, if the operations performed in <expression> leave a non-zero item at the top of the stack, then the code in <True action> will be executed. Otherwise the script will jump to the opcode immediately after OP_ENDIF.

The alternative to OP_IF is OP_NOTIF.

<Expression>

OP_NOTIF

    <False action>

OP_ENDIF


When using OP_NOTIF, if the operations performed in <expression> leave a zero-value item at the top of the stack, then the code in <False action> will be executed. Otherwise the script will jump to the opcode immediately after OP_ENDIF.

Bitcoin grammar rules require that every OP_IF / OP_NOTIF must have a corresponding OP_ENDIF.

Using OP_ELSE

Any IF statement can contain an ELSE statement which will cause the script to branch into one of two paths depending on the outcome of the expression being evaluated.

<Expression>

OP_IF

    <True action>

OP_ELSE

    <False action>

OP_ENDIF

Multi-condition loops / Case statements

Using OP_ELSE, IF statements can be nested allowing for complex nested functions to be developed. This allows for similar functionality to a case statement to be implemented.

<Case 1 check>

OP_IF

    <When 1 action>

OP_ELSE

    <Case 2 check>

    OP_IF

        <When 2 action>

    OP_ELSE

        <Else action>

    OP_ENDIF

OP_ENDIF


An alternative method to nested IF statements is repeating IF statements in the code, although care must be taken to ensure that any 'ELSE' case is captured in an IF statement separately.

<Case 1 check>

OP_IF

    <When 1 action>

OP_ENDIF

<Case 2 check>

OP_IF

    <When 2 action>

OP_ENDIF

<else check>

OP_IF

    <Else action>

OP_ENDIF