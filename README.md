# Image to PDF using WASM

```Web Assembly``` allow you to run your C/C++/Rust program in the browser.

We use [ImageMagicks](https://github.com/KnicKnic/WASM-ImageMagick) WASM library to convert our images to pdf.


## Available Scripts

### npm run start

Runs the app in the development mode.
Open http://localhost:8080 to view it in the browser.

The page will reload if you make edits.
You will also see any lint errors in the console.

### npm run build

Builds a static copy of your site to the `build/` folder.
Your app is ready to be deployed!


## How it works

    +-------------+        +-----------------+            +---------------------+
    |  Browser    +------->+   Javascript    +------------+  ImageMagicks WASM  +--------> $> convert
    +-------------+        +-----------------+            +---------------------+




