<script>
  export let folders;
  export let files;
</script>

<style>

</style>

<article class="panel">
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
</article>
