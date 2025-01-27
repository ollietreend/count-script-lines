<script>
  let input = "";
  $: output = countLines(input);

  const countLines = (text) => {
    const linesByCharacter = {};

    const spokenLine = /^([a-zA-Z0-9-\s]+):(.+)$/;

    const lines = text.split("\n").map((line) => line.trim());

    lines.forEach((line) => {
      const matches = line.match(spokenLine);
      if (!matches) return;

      const character = matches[1].trim();

      const words = matches[2]
        .replace(/\(.*?\)/g, "") // Remove text in brackets - these are stage directions
        .trim()
        .split(" ")
        .filter((word) => word !== "");

      if (!linesByCharacter[character]) {
        linesByCharacter[character] = { lines: 0, words: 0 };
      }

      linesByCharacter[character].lines++;
      linesByCharacter[character].words += words.length;
    });

    return linesByCharacter;
  };

  $: totalCharacters = Object.keys(output).length;
  $: totalLines = Object.values(output).reduce(
    (total, { lines }) => total + lines,
    0
  );
  $: totalWords = Object.values(output).reduce(
    (total, { words }) => total + words,
    0
  );
</script>

<div class="container">
  <div class="output">
    <h1>Count lines for characters in a script</h1>
    {#if input.trim() == ""}
      <p>
        Paste a script to see the number of lines and words each character has.
      </p>
    {:else if totalCharacters == 0}
      <p>No characters found in the text.</p>
    {:else}
      <p>This script has <strong>{totalCharacters} characters</strong>:</p>
      <table>
        <thead>
          <tr>
            <th>Character</th>
            <th>Lines</th>
            <th>Words</th>
          </tr>
        </thead>
        <tbody>
          {#each Object.entries(output) as [character, { lines, words }]}
            <tr>
              <td>{character}</td>
              <td>{lines}</td>
              <td>{words}</td>
            </tr>
          {/each}
        </tbody>
        <tfoot>
          <tr>
            <td>Total</td>
            <td>{totalLines}</td>
            <td>{totalWords}</td>
          </tr>
        </tfoot>
      </table>
    {/if}
  </div>
  <textarea class="input" bind:value={input} placeholder="Paste here..."
  ></textarea>
</div>

<style>
  .container {
    margin: 1rem;
    display: block;
  }
  @media (min-width: 40rem) {
    .container {
      display: flex;
      flex-grow: 1;
      margin: 2rem;
      gap: 2rem;
    }
    .output {
      flex-basis: 26rem;
      flex-grow: 0;
    }

    .input {
      flex-grow: 1;
      flex-basis: 26rem;
    }
  }
  table {
    width: 100%;
  }
  tfoot {
    font-weight: bold;
  }
  h1 {
    margin-top: 0;
  }
  textarea {
    min-height: 6em;
  }
</style>
