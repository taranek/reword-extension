<script lang="ts">
  import { onMount } from "svelte";
  import WiRefresh from "svelte-icons/wi/WiRefresh.svelte";
  import Loading from "./Loading.svelte";

  let word: Word = { word: "Loading a word..." };
  let definition: string[] = [];
  let loading = false;
  type WordInfo = {
    meanings: WordMeaning[];
  };
  type WordMeaning = {
    definitions: {
      definition: string;
      antonyms: string[];
      synonyms: string[];
    }[];
  };
  type Word = {
    word: string;
    data?: WordInfo[];
  };

  function extractDefinition(word: Word) {
    try {
      return word?.data[0].meanings[0].definitions.map((d) => d.definition);
    } catch (e) {
      console.error(e);
      definition = "No definition provided";
    }
  }
  function loadWord() {
    loading = true;
    fetch("http://localhost:3000")
      .then((d) => d.json())
      .then((loadedWord: Word) => {
        word = loadedWord;
        definition = extractDefinition(loadedWord);
      })
      .catch((e) => {
        console.error("Error occured", e);
      })
      .finally(() => {
        loading = false;
      });
  }

  onMount(() => {
    loadWord();
  });
</script>

<div class="container">
  <div class={`card ${loading ? "loading" : "loaded"}`}>
    {#if loading}<Loading /> {/if}

    {#if word && !loading}<div class="card-title">
        <h2 class="">{word?.word}</h2>
        <div on:click={loadWord} class="icon refresh-icon">
          <WiRefresh />
        </div>
      </div>

      <ul>
        {#each definition as def}
          <li>{def}</li>
        {/each}
      </ul>
    {/if}
  </div>
</div>

<style>
  .container {
    --bg-color: #111;
    --text-color: #aaa;
    --card-bg-color: rgba(255, 255, 255, 0.06);
    --card-bg-color-raised: rgba(255, 255, 255, 0.07);
    --card-border-color: rgba(255, 255, 255, 0.1);
    --card-border-color-raised: rgba(255, 255, 255, 0.12);

    --card-border-radius: 16px;
    --card-shadow: 2px 2px 8px rgba(0, 0, 0, 0.2);
    --card-shadow-raised: 8px 8px 12px rgba(0, 0, 0, 0.15),
      -8px -8px 8px rgba(0, 0, 0, 0.1);
    margin: -8px;
    min-height: 100vh;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    font-size: 30px;
    padding: 5vw;
    background: var(--bg-color);
    color: var(--text-color);
    font-family: "Inter", sans-serif;
  }

  .card {
    padding: 20px 40px;
    background: var(--card-bg-color);
    border-radius: var(--card-border-radius);
    border: 1px solid var(--card-border-color);
    box-shadow: var(--card-shadow);
    transition: flex-basis 500ms ease-in-out, opacity 200ms linear;
    display: flex;
    flex-direction: column;
    justify-content: center;
    flex-basis: 10em;
    width: 80vw;
    opacity: 1;
  }

  .card.loading {
    opacity: 0.4;
    flex-basis: 100%;
  }
  .card.loaded {
    flex-basis: 0%;
  }

  .card li {
    font-weight: 200;
    font-size: 20px;
    margin-bottom: 16px;
  }

  .card:hover {
    box-shadow: var(--card-shadow-raised);
    background: var(--card-bg-color-raised);
    border-color: var(--card-border-color-raised);
  }

  .card-title {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
  }
  button {
    border-radius: 2px;
    box-shadow: 0 1px 4px rgba(0, 0, 0, 0.6);
    background-color: #2ecc71;
    color: #ecf0f1;
    transition: background-color 0.3s;
    padding: 5px 10px;
    border: none;
  }

  button:hover,
  button:focus {
    background-color: #27ae60;
  }

  .success {
    color: #2ecc71;
    font-weight: bold;
  }
  .icon {
    width: 64px;
    height: 64px;
  }
  .refresh-icon {
    cursor: pointer;
    border-radius: 32px;
    transform: rotate(0deg);
    border: 1px solid transparent;
    transition: 0.25s linear all;
  }
  .refresh-icon:hover {
    background: rgba(255, 255, 255, 0.03);
    border: 1px solid rgba(255, 255, 255, 0.06);
  }
</style>
