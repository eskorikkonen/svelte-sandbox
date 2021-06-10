<script>
	import { fade } from 'svelte/transition';

  export let visible = true;
</script>

{#if visible}
  <h1 out:fade="{{duration: 5000}}">
    <slot></slot>
  </h1>
{/if}

<style>
h1 {
  color: black;
  background-color: rgba(255, 255, 255, 0.1);
  text-transform: uppercase;
  font-size: 4em;
  font-weight: 100;
  z-index: 1;
}
</style>