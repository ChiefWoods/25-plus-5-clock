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
  let title = writable("25 + 5 Clock");
  let isReset = true;
  let interval: number;
  let audio: HTMLAudioElement;

  $: timeInSecs = $sessionMinute * 60;
  $: timeLeft = formatTime(timeInSecs);

  $: if (isReset) {
    $title = "25 + 5 Clock";
  } else if ($isPaused) {
    $title = `${timeLeft} - ${$isBreak ? "Break" : "Session"} Paused`;
  } else {
    $title = `${timeLeft} - ${$isBreak ? "Break Time!" : "Session Up"}`;
  }

  setContext("isPaused", isPaused);

  function formatTime(seconds: number) {
    let minute: number = Math.floor(seconds / 60);
    let second: number = seconds % 60;

    const time = `${minute < 10 ? "0" + minute : minute}:${
      second < 10 ? "0" + second : second
    }`;

    return time;
  }

  function playPauseTimer() {
    $isPaused = !$isPaused;
    isReset = false;

    if ($isPaused) {
      clearInterval(interval);
      audio.pause();
    } else {
      if ($isBreak) {
        audio.play();
      }

      interval = setInterval(() => {
        if (!$isBreak && timeInSecs < 1) {
          // end of session
          audio.play();
          $isBreak = !$isBreak;
          timeInSecs = $breakMinute * 60;
        } else if ($isBreak && timeInSecs < 1) {
          // end of break
          audio.pause();
          $isBreak = !$isBreak;
          timeInSecs = $sessionMinute * 60;
        } else {
          timeInSecs--;
        }
      }, 1000);
    }
  }

  function resetTimer() {
    $breakMinute = 5;
    $sessionMinute = 25;
    $isBreak = false;
    $isPaused = true;
    isReset = true;
    timeInSecs = $sessionMinute * 60;
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
      <p id="time-left" class={$isBreak ? "break" : ""}>{timeLeft}</p>
    </div>
    <div id="adjuster-container">
      <Adjuster type="break" minute={breakMinute} />
      <hr />
      <Adjuster type="session" minute={sessionMinute} />
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
<svelte:head>
  <title>{$title}</title>
</svelte:head>

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
    width: 600px;
  }

  #timer-container {
    display: flex;
    flex-direction: column;
    width: 100%;
    text-align: center;
  }

  #timer-label {
    font-family: DS-Digital;
    font-size: 4rem;
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

  @media (max-width: 600px) {
    section {
      flex-direction: column;
      width: 90%;
    }

    #timer-label {
      font-size: 3rem;
    }

    #time-left {
      font-size: 5.5rem;
    }
  }
</style>
