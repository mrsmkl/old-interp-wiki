## Initialization

Initialization:
* Initialize memory
* Initialize global variables
* Initialize call tables: an index can be used to call any function from the call table.

## Pre-processing

There are two reasons why the WebAssembly code is pre-processed before it is interpreted:
* Calculating jumps: for example if we have an `if` operation in the bytecode, to execute it efficiently, we need a way to jump to the else branch and the end of `if`-statement.
* Stack adjustment: We only have one stack for evaluation, it also includes local variables. For example if a function returns, it will need to pop the local variables and function parameters from the stack, and move the returned values to replace them. Perhaps stack could be implemented as a linked list, but then there might be problems with allocation and merkle proofs.

## Decoding

Another step that is not implemented in the on-chain interpreter is decoding instructions. This means that each the action of each phase in the execution of an instruction is given separately. This step could be implemented in the on-chain interpreter to save storage space. One complication would be that instructions would have different sizes because some need immediate values.

