<style>

</style>

<script>
    import { readFiles as readFilesStore } from '../store.js'
    import Title from '../Components/Title.svelte'
    import TopNavbar from '../Components/TopNavbar.svelte'
    import FileViewer from '../Components/FileViewer.svelte'
    import FileNavbar from '../Components/FileNavbar.svelte'
    const { dialog, Menu } = require('electron').remote
    const fs = require('fs')

    const isMac = process.platform === 'darwin'

    const template = [
        // { role: 'appMenu' }
        ...(isMac
            ? [
                  {
                      label: app.name,
                      submenu: [
                          { role: 'about' },
                          { type: 'separator' },
                          { role: 'services' },
                          { type: 'separator' },
                          { role: 'hide' },
                          { role: 'hideothers' },
                          { role: 'unhide' },
                          { type: 'separator' },
                          { role: 'quit' },
                      ],
                  },
              ]
            : []),
        // { role: 'fileMenu' }
        {
            label: 'File',
            submenu: [
                {
                    label: 'Open',
                    role: 'open',
                    click() {
                        clickEvent()
                    },
                },
                isMac ? { role: 'close' } : { role: 'quit' },
            ],
        },
    ]

    const menu = Menu.buildFromTemplate(template)
    Menu.setApplicationMenu(menu)

    let readFiles = []
    let folders = []
    let files = []

    const mode = 'picture-view'

    const unsubscribe = readFilesStore.subscribe((value) => {
        readFiles = value
        folders = getFolders(readFiles)
        files = getFiles(readFiles)
    })

    function clickEvent() {
        dialog
            .showOpenDialog({
                properties: ['openFile', 'openDirectory'],
            })
            .then(readDirectoryFiles)
    }

    function readDirectoryFiles(fileInformation) {
        const { filePaths } = fileInformation
        if (filePaths && filePaths[0]) {
            fs.readdir(filePaths[0], (err, items) => {
                const folders = items.map((element) => {
                    const fullPath = `${filePaths[0]}/${element}`
                    const isDirectory = fs.lstatSync(fullPath).isDirectory()
                    const extension = isDirectory ? '' : getFileExtension(element)

                    return {
                        element,
                        path: filePaths[0],
                        fullPath,
                        fileType: isDirectory ? 'folder' : 'file',
                        extension,
                    }
                })
                // Saves it on the global storage
                readFilesStore.set(folders)
            })
        } else {
            console.log('No folders found')
            if (!readFilesStore) {
                readFilesStore.set([])
            }
        }
    }

    function getFolders(arrayOfFiles) {
        return arrayOfFiles.filter((element) => element.fileType === 'folder')
    }

    function getFiles(arrayOfFiles) {
        return arrayOfFiles.filter((element) => element.fileType === 'file')
    }

    function getFileExtension(element) {
        const regexExtension = /\.[0-9a-z]+$/i
        return element.match(regexExtension)[0]
    }

    function openFolder(folderPath) {
        console.log(folderPath)
        fs.readdir(folderPath, (err, items) => {
            const folders = items.map((element) => {
                const fullPath = `${folderPath}/${element}`
                const isDirectory = fs.lstatSync(fullPath).isDirectory()
                const extension = isDirectory ? '' : getFileExtension(element)

                return {
                    element,
                    path: folderPath,
                    fullPath,
                    fileType: isDirectory ? 'folder' : 'file',
                    extension,
                }
            })
            // Saves it on the global storage
            readFilesStore.set(folders)
        })
    }
</script>

<main>
    <!-- <TopNavbar {clickEvent} /> -->
    <Title title="Photo Albums" subTitle="A place to organize all the folders you have in your computer" />
    <!-- <button on:click="{clickEvent}">Open folder</button> -->

    <FileNavbar />
    <FileViewer {openFolder} {folders} {files} selectedView="{mode}" />

</main>
