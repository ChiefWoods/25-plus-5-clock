<script lang="ts">
  import increment from "../assets/icons/chevron-up.svg";
  import decrement from "../assets/icons/chevron-down.svg";
  import Button from "./Button.svelte";
  import { createEventDispatcher, getContext } from "svelte";
  import type { Writable } from "svelte/store";

  export let type: string;
  export let minute: Writable<number>;

  const dispatch = createEventDispatcher();

  const isPaused: Writable<boolean> = getContext("isPaused");

  let capitalizedType = type.charAt(0).toUpperCase() + type.slice(1);
  let isMouseDown: Boolean = false;
  let interval: number;

  function handleIncrementClick() {
    incrementMinute();
    dispatch("click");
  }

  function handleDecrementClick() {
    decrementMinute();
    dispatch("click");
  }

  function incrementMinute() {
    if ($isPaused && $minute < 60) {
      $minute++;
    }
  }

  function decrementMinute() {
    if ($isPaused && $minute > 1) {
      $minute--;
    }
  }

  function startAuto(autoFunc: Function) {
    isMouseDown = true;

    setTimeout(() => {
      if (isMouseDown) {
        clearInterval(interval);
        interval = setInterval(autoFunc, 100);
      }
    }, 1000);
  }

  function stopAuto() {
    isMouseDown = false;
    clearInterval(interval);
  }
</script>

<div class="adjuster">
  <p id="{type}-label" class="label">{capitalizedType} Length</p>
  <div class="length-container">
    <p id="{type}-length" class="length">{$minute}</p>
    <div class="btn-container">
      <Button
        id="{type}-increment"
        src={increment}
        alt="Increment"
        on:click={handleIncrementClick}
        on:mousedown={() => startAuto(incrementMinute)}
        on:mouseup={stopAuto}
      />
      <Button
        id="{type}-decrement"
        src={decrement}
        alt="Decrement"
        on:click={handleDecrementClick}
        on:mousedown={() => startAuto(decrementMinute)}
        on:mouseup={stopAuto}
      />
    </div>
  </div>
</div>

<style>
  .adjuster {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 7.5px;
  }

  .label {
    font-family: DS-Digital;
    font-size: 2.5rem;
    white-space: nowrap;
  }

  .length-container {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .length {
    font-family: DS-Digital;
    font-size: 3.5rem;
  }

  .btn-container {
    display: flex;
    flex-direction: column;
    border-radius: 5px;
    border: 2px solid black;
  }

  @media (max-width: 600px) {
    .label {
      font-size: 2rem;
    }
  }
</style>
