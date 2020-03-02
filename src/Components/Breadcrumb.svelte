<style>

</style>

<script>
    export let breadcrumbArray
    export let openFolder
</script>

<nav class="breadcrumb" aria-label="breadcrumbs">
    <ul>
        {#each breadcrumbArray as { name, route }}
            <li>
                <a
                    href="#"
                    on:click="{() => {
                        openFolder(route)
                    }}"
                >
                    {name}
                </a>
            </li>
        {/each}

    </ul>
</nav>
