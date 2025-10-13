<script lang="ts">
  import "../app.css";
  import favicon from "$lib/assets/favicon.svg";

  const { children } = $props();
</script>

<svelte:head>
  <link rel="icon" href={favicon} />
  <title>Kuna söök?</title>
</svelte:head>

{@render children?.()}
