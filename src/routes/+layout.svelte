<script lang="ts">
  import '../app.css'

  let { children } = $props()
</script>

<svelte:head>
  <title>7GUIs</title>
</svelte:head>

{@render children()}