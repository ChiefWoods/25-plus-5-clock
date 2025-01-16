<script lang="ts">
  let {
    id,
    src,
    alt,
    onclick,
    onmousedown,
    onmouseup,
  }: {
    id: string;
    src: string;
    alt: string;
    onclick: () => void;
    onmousedown?: () => void;
    onmouseup?: () => void;
  } = $props();
</script>

<button {id} {onclick} {onmousedown} {onmouseup}>
  <img {src} {alt} />
</button>

<style>
  button {
    background: none;
    border: none;
    cursor: pointer;

    &:hover {
      scale: 1.2;
    }
  }

  img {
    width: 25px;
    aspect-ratio: 1/1;
  }
</style>
