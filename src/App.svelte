<script>
  // Copyright 2020 Bhautik Chudasama
  // 
  // Licensed under the Apache License, Version 2.0 (the "License");
  // you may not use this file except in compliance with the License.
  // You may obtain a copy of the License at
  // 
  //     http://www.apache.org/licenses/LICENSE-2.0
  // 
  // Unless required by applicable law or agreed to in writing, software
  // distributed under the License is distributed on an "AS IS" BASIS,
  // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  // See the License for the specific language governing permissions and
  // limitations under the License.

  let controller = {
    loaded: false,
    converted: true,
  };

  /**
   * Convert file to byte
   * @param file {File}
   * @returns byte {Promise<Byte>}
   */
  function fileToByteArray(file) {
    return new Promise((resolve, reject) => {
      try {
        let reader = new FileReader();
        let fileByteArray = [];
        reader.readAsArrayBuffer(file);
        reader.onloadend = (evt) => {
          if (evt.target.readyState == FileReader.DONE) {
            let arrayBuffer = evt.target.result,
              array = new Uint8Array(arrayBuffer);
            resolve(array);
          }
        };
      } catch (e) {
        reject(e);
      }
    });
  }

  // Load WASM
  import * as Magick from "https://cdn.jsdelivr.net/npm/wasm-imagemagick/dist/bundles/magickApi.js";

  controller.loaded = true;

  // User selected files
  let files = null;

  /**
   * Invoke when convert button clicked
   */
  let convert = async () => {
    document.getElementById("file").click();
    return;
  };

  /**
   * Invoke when select file change state
   */
  let selected = async (event) => {
    let files = event.target.files;
    if (files.length <= 0) return;
    controller.converted = false;
    // Byte array of selected files
    let fetchedFile = [];
    // Fetch bytes of file one by one
    for (let file of files) {
      let data = await fileToByteArray(file);
      let name = file.name;
      fetchedFile.push({ name: name, content: data });
    }
    // Get File name
    let filesName = fetchedFile.map((file) => file.name);
    const command = [`convert`];
    // Put arguments in command one by one
    for (let item of filesName) {
      command.push(item);
    }
    command.push("-resize"); // A4 paper size
    command.push("575x823");
    command.push("-gravity");
    command.push("center");
    command.push("-background");
    command.push("white");
    command.push("-extent");
    command.push("595x842");
    command.push(`${Date.now()}.pdf`); // Convert to PDF
    console.log(command);
    // Convert to pdf
    let processedFiles = await Magick.Call(fetchedFile, command);
    let blob = new Blob([processedFiles[0].blob], { type: "application/pdf" });
    var url = URL.createObjectURL(blob);
    // Open PDF URN.
    window.open(url);
    controller.converted = true;
  };
</script>

<style>
  .header {
    width: 100%;
    height: 56px;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #c2185b;
    color: #fff;
    font-family: "Teko", sans-serif;
    font-size: 22px;
  }

  .selectFile {
    padding: 22px;
    font-family: "Teko", sans-serif;
    font-size: 22px;
    border: 0;
    outline: 0;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border-radius: 5px;
  }

  .author {
    position: fixed;
    bottom: 20px;
    width: 100%;
    text-align: center;
    font-family: "Inter", serif;
    font-size: 16px;
  }

  .loader {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #fff;
  }
  .load-dot {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background: #ff5a6e;
    animation: load 300ms linear infinite alternate;
    animation-delay: 0ms;
  }
  .load-dot + .load-dot {
    margin-left: 12px;
  }
  .first {
    animation-delay: 100ms;
  }
  .second {
    animation-delay: 200ms;
  }
  .third {
    animation-delay: 300ms;
  }
  .primary {
    background: #0f1254;
  }
  @keyframes load {
    100% {
      transform: scale(1.6);
    }
  }
</style>

<div>
  {#if !controller.loaded || !controller.converted}
    <div class="loader">
      <div class="load-dot primary" />
      <div class="load-dot primary first" />
      <div class="load-dot primary second" />
      <div class="load-dot third" />
    </div>
  {:else}
    <header class="header">Convert Image to PDF</header>
    <input
      on:change={selected}
      bind:files
      type="file"
      multiple
      hidden
      id="file"
      accept="image/x-png,image/png,image/jpeg,image/jpg" />
    <div>
      <button type="button" on:click={convert} class="selectFile">Select File</button>
    </div>
    <footer class="author">A Experiment of Bhautik Chudasama</footer>
  {/if}
</div>
