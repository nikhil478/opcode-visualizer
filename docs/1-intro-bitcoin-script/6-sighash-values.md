| **Flag**                          | **SIGHASH Value**          | **Functional Meaning**                                           |
|-----------------------------------|----------------------------|------------------------------------------------------------------|
| **SIGHASH_ALL**                   | `0x41 / 0100 0001`         | Sign all inputs and outputs                                      |
| **SIGHASH_NONE**                  | `0x42 / 0100 0010`         | Sign all inputs and no output                                    |
| **SIGHASH_SINGLE**                | `0x43 / 0100 0011`         | Sign all inputs and the output with the same index               |
| **SIGHASH_ALL | ANYONECANPAY**    | `0xC1 / 1100 0001`         | Sign its own input and all outputs                               |
| **SIGHASH_NONE | ANYONECANPAY**   | `0xC2 / 1100 0010`         | Sign its own input and no output                                 |
| **SIGHASH_SINGLE | ANYONECANPAY** | `0xC3 / 1100 0011`         | Sign its own input and the output with the same index            |