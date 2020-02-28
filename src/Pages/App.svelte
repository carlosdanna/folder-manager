<script>
  const { dialog } = require("electron").remote;
  const fs = require("fs");

  function clickEvent() {
    dialog
      .showOpenDialog({
        properties: ["openFile", "openDirectory"]
      })
      .then(({ filePaths }) => {
        if (filePaths && filePaths[0]) {
          fs.readdir(filePaths[0], (err, items) => {
            const folders = items.map(element => {
              const isDirectory = fs
                .lstatSync(`${filePaths[0]}/${element}`)
                .isDirectory();
              let extension = "";
              if (!isDirectory) {
                const regexExtension = /\.[0-9a-z]+$/i;
                extension = element.match(regexExtension);
              }
              return {
                element,
                path: filePaths[0],
                fullPath: `${filePaths[0]}/${element}`,
                type: isDirectory ? "folder" : "file",
                extension: extension ? extension[0] : ""
              };
            });
            console.log(folders);
          });
        } else {
          console.log("No folders found");
        }
      });
  }

  export let name;
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <h1>Hello {name}!</h1>
  <p>
    Visit the
    <a href="https://svelte.dev/tutorial">Svelte tutorial</a>
    to learn how to build Svelte apps...
  </p>
  <button on:click={clickEvent}>Open folder</button>
</main>
