<script lang="ts">
  import Button from "./Button.svelte";

  let {
    id,
    src,
    alt,
    minuteFunc,
  }: {
    id: string;
    src: string;
    alt: string;
    minuteFunc: () => void;
  } = $props();

  let isMouseDown = $state(false);
  let speed = $state(500);

  $effect(() => {
    const interval = setInterval(() => {
      if (isMouseDown) {
        minuteFunc();
        speed = Math.max(50, speed - 50);
      } else {
        speed = 500;
      }
    }, speed);

    return () => {
      clearInterval(interval);
    };
  });
</script>

<Button
  {id}
  {src}
  {alt}
  onclick={minuteFunc}
  onmousedown={() => (isMouseDown = true)}
  onmouseup={() => (isMouseDown = false)}
/>
