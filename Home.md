# Introduction

This repo has a fork of the WebAssembly reference interpreter that has been modified to be able to generate Merkle proofs that are needed to verify off-chain computations. The actual execution of the WebAssembly programs is different too, as described [here](Initializing-and-preprocessing-WebAssembly).

## Command line options

```
Usage: wasm [option] [file ...]
  -                 run interactively (default if no files given)
  -e                evaluate string
  -i                read script from file
  -o                write module to file
  -w                configure output width (default is 80)
  -s                show module signatures
  -u                unchecked, do not perform validation
  -h                exclude harness for JS convesion
  -d                dry, do not run program
  -t                trace execution
  -v                show version
  -trace-stack      trace execution stack
  -m                merkle proof mode
  -micro            merkle proof mode (microsteps)
  -merkletest       just run a merkle root computation test with a number of leafs
  -init             output initial state hash of a test case
  -init-vm          output initial vm of a test case
  -result           output final state hash of a test case and the number of steps
  -case             for which test case the hash or proofs will be generated
  -location         for which step the hash will be generated
  -step             for which step the proofs will be generated
  -error-step       for which step the intermediate state will be generated
  -final            generate finality proof for the specified step
  -insert-error     insert a simple error so that verifier and solver will disagree
  -input-file       set the file that contains input from the blockchain
  -memory-size      how many pages the size of the memory should be. One page is 64kb
  -table-size       how many elements should the call table have. Default 64
  -globals-size     how many elements should the globals table have. Default 64
  -stack-size       how many elements should the stack have. Default 16384
  -call-stack-size  how many elements should the call stack have. Default 1024
  -wasm             run main function from this file
  -help             Display this list of options
```
