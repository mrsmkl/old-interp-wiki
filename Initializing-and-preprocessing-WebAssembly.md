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

## Description of special instructions

There are following special instructions:
* `EXIT`: Normal exit.
* `JUMP`: Static jump to a position in code.
* `JUMPI`: Conditional jump to a position in code. Jumps if the top element in stack is not `0`.
* `JUMPFORWARD n`: Use a jump table with size `n`.
* `LABEL`: Used to resolve the jumps.
* `RETURN`: Returns from function, this means that it takes a value from the call stack and jumps to that location.
* `DROP`: Can drop many values from stack.
* `DUP n`: Duplicates the `n`th element from stack to the top. Used for getting local variables.
* `SWAP n`: Replaces the `n`th element in the stack with the top element. Used for setting local variables.
* `CHECKCALLI`: Check that the type of the function that will be called indirectly is correct.
* `INPUTSIZE n`: Get the size of `n`th file.
* `INPUTNAME n i`: Get the `i`th byte of the name of the `n`th file.
* `INPUTDATA n i`: Get the `i`th byte of the `n`th file.
* `OUTPUTSIZE n sz`: Set the size of `n`th file. Also sets the bytes all to zero.
* `OUTPUTNAME n i v`: Set the `i`th byte of the name of the `n`th file.
* `OUTPUTDATA n i v`: Set the `i`th byte of the `n`th file.
