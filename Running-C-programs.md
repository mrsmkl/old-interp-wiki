It is now possible to use emscripten to produce `.wasm` files that can be ran with our interpreter.

First you have to install emscripten. Using emscripten SDK didn't work for me, so I built from sources:
http://kripken.github.io/emscripten-site/docs/building_from_source/index.html

Basically you have to build `fastcomp` (http://kripken.github.io/emscripten-site/docs/building_from_source/building_fastcomp_manually_from_source.html), and then modify the configuration file `.emscripten`. Also install binaryen (https://github.com/WebAssembly/binaryen) and get the main emscripten repo (https://github.com/kripken/emscripten/) and put it in your PATH.

Configuration file `.emscripten` should look something like this:
```
EMSCRIPTEN_ROOT = os.path.expanduser(os.getenv('EMSCRIPTEN') or '/home/sami/emscripten') # directory
LLVM_ROOT = os.path.expanduser(os.getenv('LLVM') or '/home/sami/emscripten-fastcomp/build/bin') # directory
BINARYEN_ROOT = os.path.expanduser(os.getenv('BINARYEN') or '/home/sami/binaryen') # directory
```

Assume that `emcc` is in your `PATH` and your C program is at `main.c`. Then you can type
```
emcc -s -s WASM=1 main.c
```

If it complains about not finding `wasm.js-pre.js`, create it using
```
cp ~/src/js/binaryen.js-pre.js ~/binaryen/src/js/wasm.js-pre.js
cp ~/src/js/binaryen.js-post.js ~/binaryen/src/js/wasm.js-post.js
```
(These seem to be wrong, but they are only needed for running it in the browser)

Compiling the C file with emscripten will create two files, `a.out.wast` and `a.out.wasm`. `a.out.wast` won't work because binaryen and reference interpreter disagree about WAST syntax. See also https://github.com/kripken/emscripten/wiki/WebAssembly

The WASM file can be ran with our interpreter by using:
```
wasm -m -wasm a.out.wasm
```
It should run the main function from the C file. Use trace option `-t` to see what happened.
```
wasm -t -m -wasm a.out.wasm
```
