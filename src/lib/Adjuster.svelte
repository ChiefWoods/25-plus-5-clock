<script lang="ts">
  import increment from "../assets/icons/chevron-up.svg";
  import decrement from "../assets/icons/chevron-down.svg";
  import MinuteButton from "./MinuteButton.svelte";

  let {
    type,
    isPaused,
    minute = $bindable(),
  }: {
    type: string;
    isPaused: boolean;
    minute: number;
  } = $props();

  function incrementMinute() {
    if (isPaused && minute < 60) {
      minute++;
    }
  }

  function decrementMinute() {
    if (isPaused && minute > 1) {
      minute--;
    }
  }
</script>

<div class="adjuster">
  <p id="{type}-label" class="label">{type.toUpperCase()} LENGTH</p>
  <div class="length-container">
    <p id="{type}-length" class="length">{minute}</p>
    <div class="btn-container">
      <MinuteButton
        id="{type}-increment"
        src={increment}
        alt="Increment"
        minuteFunc={incrementMinute}
      />
      <MinuteButton
        id="{type}-decrement"
        src={decrement}
        alt="Decrement"
        minuteFunc={decrementMinute}
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
