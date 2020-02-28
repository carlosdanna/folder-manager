<script>
  import { readFiles as readFilesStore } from "../store.js";
  import Title from "../Components/Title.svelte";
  import TopNavbar from "../Components/TopNavbar.svelte";
  import FileViewer from "../Components/FileViewer.svelte";
  import FileNavbar from "../Components/FileNavbar.svelte";
  const { dialog } = require("electron").remote;
  const fs = require("fs");

  let readFiles = [];
  let folders = [];
  let files = [];
  const unsubscribe = readFilesStore.subscribe(value => {
    readFiles = value;
    folders = getFolders(readFiles);
    files = getFiles(readFiles);
    console.log(folders, files);
  });

  function clickEvent() {
    dialog
      .showOpenDialog({
        properties: ["openFile", "openDirectory"]
      })
      .then(readDirectoryFiles);
  }

  function readDirectoryFiles(fileInformation) {
    const { filePaths } = fileInformation;
    if (filePaths && filePaths[0]) {
      fs.readdir(filePaths[0], (err, items) => {
        const folders = items.map(element => {
          const fullPath = `${filePaths[0]}/${element}`;
          const isDirectory = fs.lstatSync(fullPath).isDirectory();
          const extension = isDirectory ? "" : getFileExtension(element);

          return {
            element,
            path: filePaths[0],
            fullPath,
            fileType: isDirectory ? "folder" : "file",
            extension
          };
        });
        // Saves it on the global storage
        readFilesStore.set(folders);
      });
    } else {
      console.log("No folders found");
      if (!readFilesStore) {
        readFilesStore.set([]);
      }
    }
  }

  function getFolders(arrayOfFiles) {
    return arrayOfFiles.filter(element => element.fileType === "folder");
  }

  function getFiles(arrayOfFiles) {
    return arrayOfFiles.filter(element => element.fileType === "file");
  }

  function getFileExtension(element) {
    const regexExtension = /\.[0-9a-z]+$/i;
    return element.match(regexExtension)[0];
  }
</script>

<style>

</style>

<main>
  <TopNavbar {clickEvent} />
  <Title
    title="Photo Albums"
    subTitle="A place to organize all the folders you have in your computer" />
  <button on:click={clickEvent}>Open folder</button>

  <FileNavbar />
  <FileViewer {folders} {files} />
  <!-- <article class="panel">
    {#each folders as { element }}
      <a class="panel-block">
        <span class="panel-icon">
          <i class="fas fa-folder" aria-hidden="true" />
        </span>
        {element}
      </a>
    {/each}
    {#each files as { element }}
      <a class="panel-block">
        <span class="panel-icon">
          <i class="fas fa-file-image" aria-hidden="true" />
        </span>
        {element}
      </a>
    {/each}
  </article> -->

</main>
