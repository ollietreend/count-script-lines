<script>
  let input = "";
  $: output = countLines(input);

  const countLines = (text) => {
    const linesByCharacter = {};

    const spokenLine = /^([a-zA-Z0-9\s]+):(.+)$/;

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
</script>

<div class="container">
  <div class="output">
    <h1>Count lines for characters in a script</h1>
    {#if input.trim() == ""}
      <p>
        Paste a script to see the number of lines and words each character has.
      </p>
    {:else if Object.keys(output).length == 0}
      <p>No characters found in the text.</p>
    {:else}
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
    max-width: 1200px;
  }
  @media (min-width: 600px) {
    .container {
      display: grid;
      grid-template-columns: 4fr 6fr;
      grid-gap: 2rem;
      margin: 2rem;
    }
  }
  table {
    width: 100%;
  }
  h1 {
    margin-top: 0;
  }
  textarea {
    min-height: 6em;
  }
</style>
