<script lang="ts">
  let min: number = 1;
  let max: number = 100;
  let randomNumber: number | null = null;
  let totalNumbers: number = 25;
  let generatedNumbers: number[] = [];
  let unusedNumbers: Set<number> = new Set(); // Track unused numbers
  let errorMessage: string | null = null;

  // Function to generate random numbers
  function generateRandomNumber(): void {
    // Validate the inputs
    if (min >= max || totalNumbers > (max - min + 1)) {
      errorMessage = 'Pienimmän ja suurimman numeron välillä ei ole tarpeeksi numeroita.';
      return;
    }

    // Clear any previous error message
    errorMessage = null;

    // Reset previous generated numbers and unused numbers
    generatedNumbers = [];
    unusedNumbers.clear();

    // Create a pool of numbers from min to max
    let availableNumbers = Array.from({ length: max - min + 1 }, (_, i) => i + min);

    // Randomly select 'totalNumbers' unique numbers
    while (generatedNumbers.length < totalNumbers) {
      const randomIndex = Math.floor(Math.random() * availableNumbers.length);
      const randomNumber = availableNumbers[randomIndex];

      // Remove the selected number from the pool
      availableNumbers.splice(randomIndex, 1);
      generatedNumbers.push(randomNumber);
    }
  }

  // Mark a number as unused and replace it with a new random number
  function markAsUnused(number: number): void {
    unusedNumbers.add(number);

    // Create a pool of available numbers excluding already generated and unused ones
    const availableNumbers = Array.from({ length: max - min + 1 }, (_, i) => i + min)
      .filter(n => !generatedNumbers.includes(n) && !unusedNumbers.has(n));

    if (availableNumbers.length > 0) {
      // Find the index of the number to replace
      const index = generatedNumbers.indexOf(number);
      const newRandomNumber = availableNumbers[Math.floor(Math.random() * availableNumbers.length)];

      // Replace the unused number with a new one
      generatedNumbers[index] = newRandomNumber;
    } else {
      alert('Kaikki numerot on jo käytetty.');
    }
  }

  // Function to reset everything
  function resetNumbers(): void {
    generatedNumbers = [];
    unusedNumbers.clear();
    errorMessage = null;
  }
</script>

<main>
  <h1>Arvontakone</h1>  

  <!-- Error message -->
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
  
  <label>Pienin arpanumero:
    <input type="number" min="1" bind:value={min}/>
  </label>
  <label>Suurin arpanumero:
    <input type="number" min="1" bind:value={max}/>
  </label>
  <label>
    Kuinka monta numeroa:
    <input type="number" min="1" bind:value={totalNumbers} />
  </label>
  
  <button on:click={generateRandomNumber}>
    Arvo {totalNumbers} voittonumero{#if totalNumbers > 1}a{/if}
  </button>

  <button on:click={resetNumbers}>
    Nollaa voittonumerot
  </button>
  
  <!-- Display the generated random number -->
  {#if generatedNumbers.length > 0}
    <h2>Voittonumerot</h2>
    <ul>
      {#each generatedNumbers as number}
      <li class:unused={unusedNumbers.has(number)}>
        {number} 
        <button class="icon-button" on:click={() => markAsUnused(number)} aria-label="Merkitse käyttämättömäksi" disabled={unusedNumbers.has(number)}>
          ❌
        </button>
      </li>
      {/each}
    </ul>
  {/if}
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-family: sans-serif;
  }

  button {
    margin: 1rem 0;
    padding: 1rem 2rem;
    font-size: 1.2rem;
    cursor: pointer;
  }

  label {
    margin: .675rem 0;
  }

  ul {
    list-style: none;
    padding: 0;
  }

  li {
    display: flex;
    align-items: center;
    gap: 2rem;
    flex-flow: row;
    justify-content: space-between;
    margin: 1rem 0;
  }

  h2 {
    margin-top: 1.25rem;
  }

  /* Styling for the small 'mark as unused' button */
  .icon-button {
    margin: 0;
    background: none;
    border: none;
    color: red;
    cursor: pointer;
    font-size: 20px;
    padding: 5px;
    line-height: 1;
  }

  .icon-button:hover {
    transform: scale(1.2);
  }

  .icon-button:focus {
    outline: 2px solid blue;
  }

  .icon-button:disabled {
    cursor: not-allowed;
    color: lightgray;
  }

  /* Gray out unused numbers */
  .unused {
    color: gray;
    text-decoration: line-through;
  }

  /* Error styling */
  .error {
    color: red;
    font-weight: bold;
  }
</style>
