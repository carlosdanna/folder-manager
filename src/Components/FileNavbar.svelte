<style>

</style>

<script>
    export let viewMode
    export let changeViewMode
</script>

<nav class="navbar" role="navigation" aria-label="main navigation">

    <div id="navbarBasicExample" class="navbar-menu">
        <!-- <div class="navbar-start">
            <a class="navbar-item">Home</a>

            <a class="navbar-item">Documentation</a>

            <div class="navbar-item has-dropdown is-hoverable">
                <a class="navbar-link">More</a>

                <div class="navbar-dropdown">
                    <a class="navbar-item">About</a>
                    <a class="navbar-item">Jobs</a>
                    <a class="navbar-item">Contact</a>
                    <hr class="navbar-divider" />
                    <a class="navbar-item">Report an issue</a>
                </div>
            </div>
        </div> -->

        <div class="navbar-end">
            <div class="navbar-item">
                <div class="buttons">
                    <button
                        class="button {viewMode === 'portrait' ? 'is-active' : ''}"
                        on:click="{changeViewMode('portrait')}"
                    >
                        <span class="icon">
                            <i class="fas fa-portrait"></i>
                        </span>
                    </button>
                    <button class="button {viewMode === 'list' ? 'is-active' : ''}" on:click="{changeViewMode('list')}">
                        <span class="icon">
                            <i class="fas fa-bars"></i>
                        </span>
                    </button>
                    <button class="button {viewMode === 'grid' ? 'is-active' : ''}" on:click="{changeViewMode('grid')}">
                        <span class="icon">
                            <i class="fas fa-th"></i>
                        </span>
                    </button>
                </div>
            </div>
        </div>
    </div>
</nav>
