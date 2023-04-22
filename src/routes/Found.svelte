<script lang="ts">
  import { receive } from "./transitions";
  import { getTwemojiYrl } from "./utils";

  export let found: string[];
</script>

<div class="found">
  {#each found as emoji (emoji)}
    <div class="pair">
      <img
        in:receive={{ key: `${emoji}:a` }}
        alt={emoji}
        src={getTwemojiYrl(emoji)}
      />
      <img
        in:receive={{ key: `${emoji}:b` }}
        alt={emoji}
        src={getTwemojiYrl(emoji)}
      />
    </div>
  {/each}
</div>

<style>
  .found {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 1.5em;
    height: 100%;
    filter: drop-shadow(0.2em 0.2em 0.5em rgba(0, 0, 0, 0.2));
  }

  .pair {
    display: flex;
    align-items: center;
    justify-content: center;

    width: 4em;
    height: 4em;
    background: white;
    padding: 1em;
    border-radius: 50%;
  }
  img {
    position: absolute;

    width: 2em;
    height: 2em;
  }
</style>
