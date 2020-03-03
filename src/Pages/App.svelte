<style>

</style>

<script>
    import { onMount } from 'svelte'
    const { dialog, Menu } = require('electron').remote
    const fs = require('fs')

    import { readFiles as readFilesStore } from '../store.js'
    import Breadcrumb from '../Components/Breadcrumb.svelte'
    import Title from '../Components/Title.svelte'
    import TopNavbar from '../Components/TopNavbar.svelte'
    import FileViewer from '../Components/FileViewer.svelte'
    import FileNavbar from '../Components/FileNavbar.svelte'
    import SideMenu from '../Components/SideMenu.svelte'

    const isMac = process.platform === 'darwin'

    let breadcrumbArray = []
    let breadcrumbPrefix = ''

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
                    accelerator: 'CmdOrCtrl+O',
                    click(menuItem, browserWindow, event) {
                        clickEvent()
                    },
                },
                {
                    type: 'separator',
                },
                isMac ? { role: 'close' } : { role: 'quit' },
            ],
        },
        {
            label: 'View',
            submenu: [
                { role: 'reload' },
                { role: 'forcereload' },
                { role: 'toggledevtools' },
                { type: 'separator' },
                { role: 'resetzoom' },
                { role: 'zoomin' },
                { role: 'zoomout' },
                { type: 'separator' },
                { role: 'togglefullscreen' },
            ],
        },
    ]

    const menu = Menu.buildFromTemplate(template)
    Menu.setApplicationMenu(menu)

    const breadcrumbsRegex = /[\/|\\][a-zA-Z0-9#\ \-_\$()\[\].,]*/gi

    let readFiles = []
    let folders = []
    let files = []
    let folderPath = []

    let showSideMenu = true

    let viewMode = 'grid'

    const unsubscribe = readFilesStore.subscribe((value) => {
        readFiles = value
        folders = getFolders(readFiles)
        files = getFiles(readFiles)
    })

    function toggleMenu() {
        showSideMenu = !showSideMenu
    }

    function changeViewMode(mode) {
        viewMode = mode
    }

    onMount(() => {
        const defaultFolder = 'C:\\Users\\cardan\\Pictures\\Anime'

        openFolder(defaultFolder)
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
            console.log(filePaths[0])
            openFolder(filePaths[0])
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
        return arrayOfFiles.filter((element) => element.fileType === 'file' && element.isImageExtension)
    }

    function getFileExtension(element) {
        if (element) {
            const regexExtension = /\.[0-9a-z]+$/gi
            if (regexExtension.test(element)) {
                const matches = element.match(regexExtension)
                return matches[matches.length - 1]
            }
        }
        return ''
    }

    function breadcrumbRoute(route) {
        if (route) {
            let breadcrumbArray = route.match(breadcrumbsRegex)
            return generateBreadcrumbRoute(route, breadcrumbArray)
        }
        return []
    }

    function checkRoutePrefix(path) {
        const routePrefixRegex = /\w:/gi
        if (routePrefixRegex.test(path)) {
            return path.match(routePrefixRegex)[0]
        }
        return ''
    }

    function generateBreadcrumbRoute(route, breadcrumbArray) {
        let routeDepht = checkRoutePrefix(route)

        return breadcrumbArray.map((element) => {
            routeDepht += element
            return {
                name: element.substr(1),
                route: routeDepht,
            }
        })
    }

    function openFolder(newPath) {
        const checkImageRegex = /\.?(jpg|gif|jpeg|jfif|png|webp)/gi
        fs.readdir(newPath, (err, items) => {
            JSON.stringify(items)
            if (items) {
                folderPath = newPath
                breadcrumbArray = breadcrumbRoute(folderPath)
                const folders = items.map((element) => {
                    const fullPath = `${folderPath}/${element}`
                    const isDirectory = fs.lstatSync(fullPath).isDirectory()
                    const extension = isDirectory ? '' : getFileExtension(element)
                    const isImageExtension = extension && extension.match(checkImageRegex)[0] ? true : false

                    return {
                        element,
                        path: folderPath,
                        fullPath,
                        fileType: isDirectory ? 'folder' : 'file',
                        extension,
                        isImageExtension,
                    }
                })
                // Saves it on the global storage
                readFilesStore.set(folders)
            } else {
                console.error('Unable to open folder')
            }
        })
    }
</script>

<main>

    <div class="columns">
        <div class="column">
            <Title
                title="Photo Albums"
                subTitle="A place to organize all the folders you have in your computer"
                openFolder="{clickEvent}"
                {toggleMenu}
            />

            <div class="container">
                <FileNavbar {viewMode} {changeViewMode} />
                <Breadcrumb {openFolder} {breadcrumbArray} />
                <FileViewer {openFolder} {folders} {files} {viewMode} />
            </div>
        </div>
        {#if showSideMenu}
            <div class="column is-3">
                <SideMenu {breadcrumbArray} />
            </div>
        {/if}
    </div>

</main>
