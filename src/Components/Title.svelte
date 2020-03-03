<style>
    .align-right {
        text-align: end;
    }
</style>

<script>
    export let title
    export let subTitle
    export let openFolder
    export let toggleMenu
</script>

<section class="hero is-dark">

    <div class="hero-body">
        <div class="align-right">
            <button class="button is-dark" on:click="{toggleMenu}">
                <i class="fas fa-bars"></i>
            </button>
        </div>
        <div class="container">
            <h1 class="title">{title}</h1>
            <h2 class="subtitle">{subTitle}</h2>
        </div>
    </div>
</section>
