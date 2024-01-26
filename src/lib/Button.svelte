<script lang="ts">
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();

  export let id: string;
  export let src: string;
  export let alt: string;
</script>

<button
  {id}
  on:click={() => dispatch("click")}
  on:mousedown={() => dispatch("mousedown")}
  on:mouseup={() => dispatch("mouseup")}
>
  <img {src} {alt} />
</button>

<style>
  button {
    background: none;
    border: none;
    cursor: pointer;
  }

  button:hover {
    scale: 1.2;
  }

  img {
    width: 25px;
    aspect-ratio: 1/1;
  }
</style>
