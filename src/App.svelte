<script lang="ts">
  import play from "./assets/icons/play.svg";
  import pause from "./assets/icons/pause.svg";
  import reset from "./assets/icons/restart.svg";
  import beep from "./assets/audio/beep.mp3";
  import Adjuster from "./lib/Adjuster.svelte";
  import Button from "./lib/Button.svelte";
  import Footer from "./lib/Footer.svelte";
  import { writable } from "svelte/store";
  import { setContext } from "svelte";

  let breakMinute = writable(5);
  let sessionMinute = writable(25);
  let isBreak = writable(false);
  let isPaused = writable(true);

  let timeLeft: number = $sessionMinute * 60;
  let timeInSecs: string;
  let interval: number;
  let audio: HTMLAudioElement;

  $: timeInSecs = formatTime(timeLeft);

  setContext("isPaused", isPaused);

  function formatTime(seconds: number) {
    let minute: number = Math.floor(seconds / 60);
    let second: number = seconds % 60;

    const time = `${minute < 10 ? "0" + minute : minute}:${
      second < 10 ? "0" + second : second
    }`;

    return time;
  }

  function handleAdjusterClick() {
    if ($isPaused) {
      timeLeft = $sessionMinute * 60;
    }
  }

  function playPauseTimer() {
    $isPaused = !$isPaused;

    if ($isPaused) {
      clearInterval(interval);
      audio.pause();
    } else {
      interval = setInterval(() => {
        if (!$isBreak && timeLeft < 1) {
          audio.play();
          $isBreak = !$isBreak;
          timeLeft = $breakMinute * 60;
        } else if ($isBreak && timeLeft < 1) {
          audio.pause();
          audio.currentTime = 0;
          $isBreak = !$isBreak;
          timeLeft = $sessionMinute * 60;
        } else {
          timeLeft--;
        }
      }, 1000);
    }
  }

  function resetTimer() {
    $breakMinute = 5;
    $sessionMinute = 25;
    $isBreak = false;
    $isPaused = true;
    timeLeft = $sessionMinute * 60;
    clearInterval(interval);
    audio.pause();
    audio.currentTime = 0;
  }
</script>

<main>
  <section>
    <div id="timer-container">
      <p id="timer-label" class={$isBreak ? "break" : ""}>
        {$isBreak ? "Break" : "Session"}
      </p>
      <p id="time-left" class={$isBreak ? "break" : ""}>{timeInSecs}</p>
    </div>
    <div id="adjuster-container">
      <Adjuster type="break" minute={breakMinute} />
      <hr />
      <Adjuster
        type="session"
        minute={sessionMinute}
        on:click={handleAdjusterClick}
      />
      <hr />
      <div id="flow">
        <Button
          id="start_stop"
          src={$isPaused ? play : pause}
          alt={$isPaused ? "Play" : "Pause"}
          on:click={playPauseTimer}
        />
        <Button id="reset" src={reset} alt="Reset" on:click={resetTimer} />
      </div>
    </div>
    <audio id="beep" src={beep} loop bind:this={audio}></audio>
  </section>
</main>
<Footer />

<style>
  @font-face {
    font-family: DS-Digital;
    src: url("./assets/fonts/ds_digital/DS-DIGI.TTF") format("truetype");
  }

  :root {
    --roman-red: #dd5353;
    --crail-red: #b73e3e;
    --eucalyptus-green: #279946;
    --gray-nurse: #e2e4e1;
  }

  main {
    flex: 1;
    display: grid;
    place-items: center;
    background: var(--roman-red);
  }

  section {
    border-radius: 40px;
    border: 25px solid var(--crail-red);
    background: var(--gray-nurse);
    padding: 30px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 30px;
    min-width: 550px;
  }

  #timer-container {
    display: flex;
    flex-direction: column;
  }

  #timer-label {
    font-family: DS-Digital;
    font-size: 4rem;
    text-align: center;
  }

  #time-left {
    font-family: DS-Digital;
    font-size: 10rem;
  }

  .break {
    color: var(--eucalyptus-green);
  }

  #adjuster-container {
    display: flex;
    flex-direction: column;
    gap: 15px;
  }

  hr {
    border-top: 2px solid black;
  }

  #flow {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
  }
</style>
