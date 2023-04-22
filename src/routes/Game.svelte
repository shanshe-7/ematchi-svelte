<script lang="ts">
  import { createEventDispatcher, onMount } from "svelte";
  import Countdown from "./Countdown.svelte";
  import Found from "./Found.svelte";
  import Grid from "./Grid.svelte";
  import type { Level } from "./levels";
  import { shuffle } from "./utils";

  const dispatch = createEventDispatcher();

  export let state: "waiting" | "playing" | "paused" | "won" | "lost" =
    "waiting";

  let size: number;
  let grid: string[] = [];
  let found: string[] = [];
  let remaining: number = 0;
  let duration: number = 0;
  let playing = false;

  export function start(level: Level) {
    size = level.size;
    grid = createGrid(level);
    remaining = duration = level.duration;
    resume();
  }

  function resume() {
    dispatch("play");
  }

  function createGrid(level: Level) {
    const copy = level.emojis.slice();
    const pairs: string[] = [];

    for (let i = 0; i < level.size ** 2 / 2; i++) {
      const idx = Math.floor(Math.random() * copy.length);
      const emoji = copy[idx];
      copy.splice(idx, 1);
      pairs.push(emoji);
    }

    pairs.push(...pairs);
    return shuffle(pairs);
  }

  function countdown() {
    const start = Date.now();
    let remainingAtStart = remaining;

    (function loop() {
      if (!playing) return;

      requestAnimationFrame(loop);

      remaining = remainingAtStart - (Date.now() - start);

      if (remaining <= 0) {
        dispatch("lose");
      }
    })();
  }

  function setInitialState(input: string) {
    if (input === "won" || input === "lost") {
      grid = [];
      found = [];
      remaining = 0;
      duration = 0;
      playing = false;
    }
    if (state === "playing") {
      playing = true;
      countdown();
    }
  }

  $: setInitialState(state);
</script>

<div class="game" style="--size: {size}">
  <div class="info">
    {#if state === "playing" || state === "paused"}
      <Countdown
        {duration}
        {remaining}
        on:click={() => {
          playing = false;
          dispatch("pause");
        }}
      />
    {/if}
  </div>

  <div class="grid-container">
    <Grid
      {grid}
      on:found={(e) => {
        found.push(e.detail.emoji);
        found = found;

        if (found.length === (size * size) / 2) {
          dispatch("win");
        }
      }}
      {found}
    />
  </div>

  <div class="info">
    <Found {found} />
  </div>
</div>

<style>
  .game {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100%;
    font-size: min(1vmin, 0.5rem);
  }

  .info {
    width: 70em;
    height: 10em;
  }

  .grid-container {
    width: 70em;
    height: 70em;
  }
</style>
