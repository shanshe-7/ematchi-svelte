<script lang="ts">
  import Game from "./Game.svelte";
  import "../styles.css";
  import Modal from "./Modal.svelte";
  import { levels } from "./levels";
  import { confetti } from "@neoconfetti/svelte";

  let state: "waiting" | "playing" | "paused" | "won" | "lost" = "waiting";

  let game: Game;
</script>

<Game
  on:play={() => {
    state = "playing";
  }}
  bind:this={game}
  on:lose={() => {
    state = "lost";
  }}
  on:pause={() => {
    state = "paused";
  }}
  on:win={() => {
    state = "won";
  }}
  {state}
/>

{#if state !== "playing"}
  <Modal>
    <header>
      <h1>E<span>match</span>i</h1>
      <p class="description">the emoji match game</p>
    </header>

    {#if state === "won" || state === "lost"}
      <p>you {state} the game</p>
    {:else if state === "paused"}
      <p>game paused</p>
    {/if}

    {#if state === "waiting" || state === "lost" || state === "won"}
      <p>Choose a level:</p>
    {/if}

    <div class="buttons">
      {#if state === "paused"}
        <button
          on:click={() => {
            state = "playing";
          }}>resume</button
        >
        <button
          on:click={() => {
            state = "lost";
          }}>quit</button
        >
      {:else}
        {#each levels as level}
          <button
            on:click={() => {
              game.start(level);
            }}>{level.label}</button
          >
        {/each}
      {/if}
    </div>
  </Modal>
{/if}

{#if state === "won"}
  <div
    class="confetti"
    use:confetti={{
      stageHeight: innerHeight,
      stageWidth: innerWidth,
    }}
  />
  <!-- content here -->
{/if}

<style>
  h1 {
    font-size: 5em;
  }

  span {
    color: purple;
  }

  header {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-evenly;
    margin-bottom: 1em;

    /* gap: 0.1rem; */
  }

  h1 {
    margin: 0;
    padding: 0;
    padding-bottom: 0.5px;
  }

  p {
    margin-top: 0;
    font-family: Grandstander;
    text-align: center;
    font-size: 1.5em;
  }
  .description {
    font-size: 1em;
  }

  .buttons {
    font-family: Grandstander;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 1em;
  }

  button {
    font-family: Grandstander;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.4em;
    cursor: pointer;
    padding: 0.3em 0.5em;
    background: #e1f5fe;
    color: black;
    border-radius: 0.2em;
    border: 0.1px solid white;
  }

  .confetti {
    position: fixed;
    width: 100%;
    height: 100%;
    left: 50%;
    top: 30%;
    pointer-events: none;
  }
</style>
