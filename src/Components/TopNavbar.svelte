<script>
  export let clickEvent;
</script>

<style>

</style>

<nav class="navbar" role="navigation" aria-label="dropdown navigation">
  <!-- <a class="navbar-item">
    Photo Album
    <img
      src="https://bulma.io/images/bulma-logo.png"
      alt="Bulma: Free, open source, and modern CSS framework based on Flexbox"
      width="112"
      height="28" /> 
  </a> -->

  <div class="navbar-item has-dropdown is-hoverable">
    <a class="navbar-link is-arrowless" href="">File</a>

    <div class="navbar-dropdown">
      <a class="navbar-item" href="#" on:click={clickEvent}>Open Folder</a>
      <hr class="navbar-divider" />
      <a class="navbar-item" href="#">Exit</a>
    </div>
  </div>
</nav>
