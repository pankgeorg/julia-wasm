# Julia on WASM - Setup instructions

This repo contains various experiments for setting up julia on wasm.
It's intended for collaboration and issue tracking before things are
working sufficiently to switch to the appropriate upstream repo:
There's two scripts in this repo:
 - `build_julia_wasm.sh` which will setup all the directories and build two copies
   of julia (one natively for cross compiling the system image, one for wasm)
 - `rebuild_js.sh` which will rebuild just the wasm parts and dump it into the website/
   directory which is a hacked up copy of https://github.com/vtjnash/JuliaWebRepl.jl

# Try it out

There's two ways to try out the current state of the wasm port without building anything yourself.
1. An instance of the Web REPL hosted at https://keno.github.io/julia-wasm/website/repl.htm
2. Using the iodide IDE plugin (see https://alpha.iodide.io/notebooks/225/ to get started).

Both use a pre-built version that it regularly pushed to this repo. However, to save space it may
be a few days out of date. Please note that this is an extremely early alpha and many things are likely
(and known) to be broken.

# To get started

First install the emscripten SDK
```
# Install emsdk
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install emscripten-incoming-64bit binaryen-master-64bit
./emsdk activate emscripten-incoming-64bit binaryen-master-64bit
```
Then build and install upstream LLVM
```
# Install upstream LLVM
git clone https://github.com/llvm/llvm-project
mkdir llvm-build
cd llvm-build
cmake -G Ninja -DLLVM_ENABLE_PROJECTS="clang;lld" -DCMAKE_BUILD_TYPE=Release ../llvm-project/llvm
ninja
# Add LLVM_ROOT = '<PATH_TO_LLVM_HERE>/llvm-build/bin' to $HOME/.emscripten
# ADD llvm-build/bin to path
```
Finally, you're ready to build julia for wasm
```
# Build julia-wasm (do this once - this command may fail at the very end of the build process, that's normal)
./build-julia-wasm.sh
```
This command may fail at the very end with an error like the following:
```
    JULIA build-native/usr/lib/julia/sys-o.a
syntax: incomplete: premature end of inputErrorException("")
ERROR: LoadError: syntax: incomplete: premature end of input
Stacktrace:
 [1] top-level scope at /root/julia-wasm/julia/contrib/generate_precompile.jl:7
```
This is expected (we're using a 32bit linux build, but telling it it's wasm to generate a compatible system image, so things are a bit confused). Afterwards, build the wasm build using: 
```
 # Do this after you change something on the wasm side
 ./rebuild_js.sh
```
Afterwards you may set up a webserver using:
```
emrun --no_browser --port 8888 website/repl.htm &
```
At the moment `Firefox Nightly` seems to have the most complete
wasm support and seems to be the fastest, so I'd recommend trying that.
After starting the server above, just navigate to `localhost:8888/repl.htm`

#### Enable WASM GC support integration (Firefox Nightly)

In `about:config` enable `javascript.options.wasm_gc`

#### Enable WASM Reference Types (Chrome Canary)

```
google-chrome-unstable  --jsflags="--experimental-wasm-anyref"
```
