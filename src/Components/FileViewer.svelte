<style>
    .grid-4 {
        display: grid;
        grid-template-columns: 25% 25% 25% 25%;
    }
    .grid-element {
        width: 100%;
        min-height: 200px;
    }

    .card {
        width: 100%;
        cursor: pointer;
    }
</style>

<script>
    export let openFolder
    export let folders
    export let files
    export let viewMode
</script>

<div>

    {#if viewMode === 'grid'}
        <div class="tile is-ancestor grid-4">
            {#each folders as { element, fullPath }}
                <div class="tile is-parent grid-element">

                    <div
                        class="card"
                        on:click="{() => {
                            openFolder(fullPath)
                        }}"
                    >
                        <div class="card-image">
                            <figure class="image is-4by3">
                                <img src="https://bulma.io/images/placeholders/1280x960.png" alt="Placeholder image" />
                            </figure>
                        </div>
                        <div class="card-content">

                            <div class="media-content">
                                <p>{element}</p>
                            </div>
                        </div>
                    </div>

                </div>
            {/each}
            {#each files as { element, fullPath }}
                <div class="tile is-parent grid-element">
                    <div class="card">
                        <div class="card-image">
                            <figure class="image is-4by3">
                                <img src="{fullPath}" alt="Placeholder image" />
                            </figure>
                        </div>
                        <div class="card-content">

                            <div class="media-content">
                                <p>{element}</p>
                            </div>
                        </div>
                    </div>
                </div>
            {/each}
        </div>
    {:else if 'portrait' === viewMode}
        <div>
            {#each files as { element, fullPath }}
                <img src="{fullPath}" alt="someImage" />
            {/each}
        </div>
    {:else}
        <article class="panel">
            <p class="panel-heading">Folders</p>
            {#each folders as { element, fullPath }}
                <a
                    class="panel-block"
                    on:click="{() => {
                        openFolder(fullPath)
                    }}"
                >
                    <span class="panel-icon">
                        <i class="fas fa-folder" aria-hidden="true"></i>
                    </span>
                    {element}
                </a>
            {/each}
            {#each files as { element }}
                <a class="panel-block">
                    <span class="panel-icon">
                        <i class="fas fa-file-image" aria-hidden="true"></i>
                    </span>
                    {element}
                </a>
            {/each}
        </article>
    {/if}
</div>
