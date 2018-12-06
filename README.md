# Wasm-Rust-demo

> A starter application for WASM and Rust.

## Prerequisites

* Curl

```shell
sudo apt-get install curl
```

* Rust

```shell
curl https://sh.rustup.rs -sSf | sh
```

* Cmake

```shell
sudo apt-get install cmake
```

## Installing

### Set up the enviroment

1. Add wasm32-unknown-emscripten compile target via rustup:

```shell
rustup target add wasm32-unknown-emscripten
```

2.Set up Emscripten via emsdk:

```shell
curl https://s3.amazonaws.com/mozilla-games/emscripten/releases/emsdk-portable.tar.gz | tar -zxv -C ~/
cd ~/emsdk-portable
./emsdk update
./emsdk install sdk-incoming-64bit
```

3. Activate Emscripten by:

Running, 
```shell
./emsdk activate sdk-incoming-64bit
```
and following the instructions to add bin directories to path, 

or

```shell
source ./emsdk_env.sh
```

_NOTE: If you dont add the bin directories to PATH, have in mind that when you run source ./emsdk_env.sh, you create a temporary enviroment, so you must not close the current terminal session._

4. Set up rust nightly into the directory of your wasm projects:

```shell
rustup override set nightly
```

5. Create project

```shell
cargo init wasm-rust-demo
```

6. Build project for broswers:

```shell
cd wasm-rust-demo
cargo build --target=wasm32-unknown-emscripten --release
```

If you run _tree target_ you will see that cargo created several files in _target/wasm32-unknown-emscripten/release/deps/_ for us. Of primary interest are the .wasm and .js files.

7. Create a site/index.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Wasm/Rust</title>
    <script>
        // This is read and used by `site.js`
        var Module = {
        wasmBinaryFile: "wasm_demo.wasm"
        }
    </script>
    <script src="site.js"></script>
</head>
<body></body>
</html>
```

8. Set an automated process to get the generated files from the _target/_ folder into the _site/_ folder, with *MakeFile*:

```shell
all:
    cargo build --target=wasm32-unknown-emscripten --release
    mkdir -p site
    find target/wasm32-unknown-emscripten/release/deps -type f -name "*.wasm" | xargs -I {} cp {} site/wasm_demo.wasm
    find target/wasm32-unknown-emscripten/release/deps -type f ! -name "*.asm.js" -name "*.js" | xargs -I {} cp {} site/site.js
```

Run _make_ and run: 

```shell
tree site
```

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.