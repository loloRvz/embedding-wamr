# Embedding WAMR

This repo documents my progress of running WASM modules from a 'native' 
C program using the wasm-c-api. 

In this example, [main.c](https://github.com/loloRvz/embedding-wamr/blob/main/main.c)
is our C program and [module.aot](https://github.com/loloRvz/embedding-wamr/blob/main/src/module.aot)
is the WASM module. Notice that it is written in C, then compiled to the
AoT wasm format. 

This example program aims to serve as a template for future applications.

## Prerequisites

To build the C program you'll need the essential C-programming tools:

```
sudo apt-get install gcc build-essential
```

Clone this repo to your workspace of choice. For our sake, we will be working
in the home directory:
```
cd
git clone https://github.com/loloRvz/embedding-wamr.git
```

### WAMR
To build and run this program you will need to clone the wasm-micro-runtime repo
and build the necessary tools:

1. Clone the [WAMR](https://github.com/bytecodealliance/wasm-micro-runtime)
repo in your home directory and set the environment variable ```WAMR_DIR``` equal to be the directory of this repo:
```
cd
git clone https://github.com/bytecodealliance/wasm-micro-runtime.git
sudo nano .bashrc
```
Add the following line at the end of your .bashrc file:
```
export WAMR_DIR="$HOME/wasm-micro-runtime"
```
Save, exit, and reload .bashrc: ```source .bashrc```

2. To be able to [build WASM applications](https://github.com/bytecodealliance/wasm-micro-runtime/blob/main/doc/build_wasm_app.md):
* Download [wasi-sdk](https://github.com/WebAssembly/wasi-sdk/releases)
and extracting the archive to the default path ```/opt/wasi-sdk```.

```
curl -L https://github.com/WebAssembly/wasi-sdk/releases/download/wasi-sdk-12/wasi-sdk-12.0-linux.tar.gz > wasi-sdk.tar.gz
tar xfv wasi-sdk.tar.gz
sudo mv wasi-sdk-12.0 /opt/wasi-sdk
rm wasi-sdk.tar.gz
```

* Install [wamrc](https://github.com/bytecodealliance/wasm-micro-runtime#build-wamrc-aot-compiler),
the AoT compiler and add it to you PATH.
```
cd $WAMR_DIR/wamr-compiler
./build_llvm.sh (this step will take a while)
mkdir build && cd build
cmake ..
make

cd
sudo nano .bashrc
```
Add the following line in your .bashrc file:
```
export PATH=$PATH:$HOME/wasm-micro-runtime/wamr-compiler/build
```
Save, exit, and reload .bashrc: ```source .bashrc```

3. Build [iwasm](https://github.com/bytecodealliance/wasm-micro-runtime/blob/main/doc/build_wamr.md)
with the WASM interpreter and AoT enabled.
```
cd $WAMR_DIR/product-mini/platforms/linux
mkdir build && cd build
cmake .. -DWAMR_BUILD_INTERP=1 -DWAMR_BUILD_AOT=1 -DWAMR_BUILD_LIBC_WASI=0 -DWAMR_BUILD_PLATFORM=linux
make
```

## Example

The current example WASM module contains the following functions:
* sum(), which takes to integer parameters and returns their sum
* callback(), which calls the C function 'callback_func' that was imported
from main.c to the WASM module. 

## Building and Running the example

```
cd $HOME/embedding-wamr/src
./build.sh
cd ..

make clean
make
./wafle src/mod.aot
```




