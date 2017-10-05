## Initialization

Initialization:
* Initialize memory
* Initialize global variables
* Initialize call tables: an index can be used to call any function from the call table.
Instead of function types we have a function type hash. This way we can be sure that indirect calls can be checked easily.

It might be useful to start from empty memory etc, and have initialization code instead, so the code would include almost all of the information.

## Pre-processing

There are two reasons why the WebAssembly code is pre-processed before it is interpreted:
* Calculating jumps: for example if we have an `if` operation in the bytecode, to execute it efficiently, we need a way to jump to the else branch and the end of `if`-statement.
* Stack adjustment: We only have one stack for evaluation, it also includes local variables. For example if a function returns, it will need to pop the local variables and function parameters from the stack, and move the returned values to replace them. Perhaps stack could be implemented as a linked list, but then there might be problems with allocation and merkle proofs.
* There is another stack for calling functions. Returning from a function is a special operation.

## Decoding

Another step that is not implemented in the on-chain interpreter is decoding instructions. This means that each the action of each phase in the execution of an instruction is given separately. This step could be implemented in the on-chain interpreter to save storage space. One complication would be that instructions would have different sizes because some need immediate values.

## Verified generation of the initial state

There can be an initial state such that given a wasm file as input, it will generate the initial state for that wasm file.
