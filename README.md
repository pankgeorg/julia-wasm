# julia-wasm

The following will get you to an installation with emscripten and llvm, assuming a clean debian-like installation

```## Starting with a clean ubuntu 20 installation
# Install cmake latest
mkdir cmake
pushd cmake
wget https://github.com/Kitware/CMake/releases/download/v3.19.1/cmake-3.19.1-Linux-x86_64.sh
./cmake-3.19.1-Linux-x86_64.sh  # Say no will just create a /bin directory
export PATH=$PATH:`pwd`/bin
popd
```

Install GCC to compile llvm
```
sudo apt update
sudo apt install build-essential ninja-build gcc-multilib g++-multilib gfortran git vim python
```
Keno's boilerplate
```
git clone https://github.com/Keno/julia-wasm.git
pushd julia-wasm
```

Latest emsdk
```
git clone https://github.com/emscripten-core/emsdk.git
pushd emsdk
# Node is not required required but in my WSL emsdk can't find system's node
./emsdk install node-12.18.1-64bit
./emsdk install emscripten-master-64bit
./emsdk install binaryen-tag-1.38.31-64bit
./emsdk activate emscripten-master-64bit binaryen-tag-1.38.31-64bit
# Add binaries to PATH
source emsdk_env.sh
popd
```

Download LLVM Sources
```
git clone https://github.com/llvm/llvm-project
```
Make a dir for the build

```
mkdir llvm-build
pushd llvm-build
```

Tell CMake where to find GCC, G++, make and create Ninja files. Rest from Keno
```
CC=`which gcc` CXX=`which g++` CMAKE_MAKE_PROGRAM=`which make` cmake -G "Ninja" -DLLVM_ENABLE_PROJECTS="clang;lld" -DCMAKE_BUILD_TYPE=Release ../llvm-project/llvm
```

Actually build llvm - this will run for some time
```
ninja
popd
```