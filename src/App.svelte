<script lang="ts">
  let run = false;
  let runText = "Run";
  let gameGrid: number[][] = Array.from({ length: 100 }, () =>
    Array.from({ length: 100 }, () => 0)
  );

  const neighbours = [
    [0, 1],
    [0, -1],
    [1, -1],
    [-1, 1],
    [1, 1],
    [-1, -1],
    [1, 0],
    [-1, 0],
  ];

  $: if (run) {
    simulate();
    runText = "Stop";
  }

  const toggleState = (i: number, j: number) => {
    const newGrid = [...gameGrid];
    newGrid[i][j] = newGrid[i][j] ? 0 : 1;
    gameGrid = newGrid;
  };

  const simulate = () => {
    if (!run) {
      runText = "Run";
      return;
    }
    const newGrid = Array.from({ length: 100 }, () =>
      Array.from({ length: 100 }, () => 0)
    );

    for (let i = 0; i < gameGrid.length; i++) {
      for (let j = 0; j < gameGrid[i].length; j++) {
        let neighbourCount = 0;
        neighbours.forEach(([x, y]) => {
          const newX = x + i;
          const newY = y + j;
          if (
            newX >= 0 &&
            newX < gameGrid.length &&
            newY >= 0 &&
            newY < gameGrid[i].length &&
            gameGrid[newX][newY] === 1
          ) {
            neighbourCount += 1;
          }
        });

        if (neighbourCount < 2 || neighbourCount > 3) {
          newGrid[i][j] = 0;
        } else if (gameGrid[i][j] === 0 && neighbourCount === 3) {
          newGrid[i][j] = 1;
        } else {
          newGrid[i][j] = gameGrid[i][j];
        }
      }
    }
    gameGrid = newGrid;
    setTimeout(simulate, 100);
  };

  const randomize = () => {
    const randomGrid = Array.from({ length: 100 }, () =>
      Array.from({ length: 100 }, () => 0)
    );
    let cellCount = 0;
    for (let i = 0; i < gameGrid.length; i++) {
      for (let j = 0; j < gameGrid[i].length; j++) {
        if (cellCount > 1200) {
          gameGrid = randomGrid;
          return;
        }
        const chance = Math.floor(Math.random() * 10);
        if (chance > 5) {
          randomGrid[i][j] = 1;
          cellCount += 1;
        }
      }
    }
  };
</script>

<main>
  <div class="game">
    <h1>Game of Life</h1>
    <div class="panel">
      <button on:click={randomize}>Randomize</button>
      <button on:click={() => (run = !run)}>{runText}</button>
    </div>
    <div class="">
      {#each gameGrid as row, i (i)}
        <div class="row">
          {#each row as cell, j (j)}
            <div
              class="cell"
              style="background-color: {cell ? 'red' : 'white'};"
              on:click={() => toggleState(i, j)}
            />
          {/each}
        </div>
      {/each}
    </div>
  </div>
</main>

<style>
  .game {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .panel {
    margin-bottom: 2rem;
  }

  .row {
    display: flex;
  }

  .cell {
    height: 0.8rem;
    width: 0.8rem;
    border: 1px solid #ccc;
    cursor: pointer;
  }

  button {
    padding: 0.5rem 1rem;
  }
</style>
