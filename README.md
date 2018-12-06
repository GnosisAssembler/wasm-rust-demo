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

2. Set up Emscripten via emsdk:

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
and following the instructions to add bin directories to PATH, 

or

```shell
source ./emsdk_env.sh
```

_NOTE: If you dont add the bin directories to PATH, have in mind that when you run source ./emsdk_env.sh, you create a temporary enviroment, so you must not close the current terminal session._

4. Set up rust nightly into the directory of your wasm projects:

```shell
rustup override set nightly
```

5. Create project:

```shell
cargo init wasm-rust-demo
```

6. Build project for broswers:

```shell
cd wasm-rust-demo
cargo build --target=wasm32-unknown-emscripten --release
```

If you run _tree target_ you will see that cargo created several files in _target/wasm32-unknown-emscripten/release/deps/_ for us. Of primary interest are the .wasm and .js files.

```shell
target
├── debug
│   ├── build
│   ├── deps
│   │   ├── wasm_demo-9155f45e2d3b2046
│   │   └── wasm_demo-9155f45e2d3b2046.d
│   ├── examples
│   ├── incremental
│   │   └── wasm_demo-qdjh4pd5y90m
│   │       ├── s-f7bzymy71w-1eipwp4-13avr2nwlpfqh
│   │       │   ├── 1h12qtepu6wjmoxz.o
│   │       │   ├── 1ki0uvre2ly2h8c8.o
│   │       │   ├── 22nnercelyg5644a.o
│   │       │   ├── 27z5p86w7wxxci0z.o
│   │       │   ├── 438cm4xgjo5x8da3.o
│   │       │   ├── dep-graph.bin
│   │       │   ├── na7a8opvk9xvjme.o
│   │       │   ├── query-cache.bin
│   │       │   └── work-products.bin
│   │       └── s-f7bzymy71w-1eipwp4.lock
│   ├── native
│   ├── wasm-demo
│   └── wasm-demo.d
├── release
│   ├── build
│   ├── deps
│   ├── examples
│   ├── incremental
│   └── native
└── wasm32-unknown-emscripten
    └── release
        ├── build
        ├── deps
        │   ├── wasm_demo.d
        │   ├── wasm-demo.js
        │   └── wasm_demo.wasm
        ├── examples
        ├── incremental
        ├── native
        ├── wasm_demo.d
        ├── wasm-demo.d
        ├── wasm-demo.js
        └── wasm_demo.wasm

```

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

Run *make* and see the tree: 

```shell
$ tree site
site
├── index.html
├── site.js
└── wasm_demo.wasm
```

9. Open a server to see the generated code:

```shell
python -m SimpleHTTPServer
```

Now go to _http://localhost:8000/site/_ and open the console.

![sample-wasm](img/sample-wasm.png?raw=true)


## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.