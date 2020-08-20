<script>
  import { onMount } from "svelte";
  import { fade } from "svelte/transition";

  let squares = [],
    width = 10,
    bombAmount = 20,
    flags = 0,
    isGameOver = false,
    isGameWon = false;

  const createBoard = () => {
    const gameArray = [
      ...Array(bombAmount).fill("bomb"),
      ...Array(width * width - bombAmount).fill("valid"),
    ];

    const shuffledArray = gameArray.sort(() => Math.random() - 0.5);

    for (let i = 0; i < width * width; i++) {
      squares.push({
        id: i,
        status: shuffledArray[i],
        checked: false,
        flagged: false,
      });
    }

    for (let i = 0; i < squares.length; i++) {
      let totalBombs = 0;
      const isLeftEdge = i % width === 0;
      const isRightEdge = i % width === width - 1;

      if (squares[i].status === "valid") {
        if (i > 0 && !isLeftEdge && squares[i - 1].status === "bomb")
          totalBombs++;
        if (i > 9 && !isRightEdge && squares[i + 1 - width].status === "bomb")
          totalBombs++;
        if (i > 10 && squares[i - width].status === "bomb") totalBombs++;
        if (i > 11 && !isLeftEdge && squares[i - 1 - width].status === "bomb")
          totalBombs++;
        if (i < 98 && !isRightEdge && squares[i + 1].status === "bomb")
          totalBombs++;
        if (i < 90 && !isLeftEdge && squares[i - 1 + width].status === "bomb")
          totalBombs++;
        if (i < 88 && !isRightEdge && squares[i + 1 + width].status === "bomb")
          totalBombs++;
        if (i < 89 && squares[i + width].status === "bomb") totalBombs++;

        squares[i].totalBombs = totalBombs;
      }
    }
  };

  createBoard();

  const handleClickSquare = (square) => {
    if (isGameOver) return;
    if (square.checked || square.flagged) return;
    if (square.status === "bomb") {
      gameOver(square);
      return;
    } else {
      if (square.totalBombs !== 0) {
        squares[square.id].checked = true;
        return;
      }
      checkSquare(square, square.id);
    }
    squares[square.id].checked = true;
    checkForWin();
  };

  const checkSquare = (square, currentId) => {
    const isLeftEdge = currentId % width === 0;
    const isRightEdge = currentId % width === width - 1;
    setTimeout(() => {
      if (currentId > 0 && !isLeftEdge) {
        const newId = currentId - 1;
        handleClickSquare(squares[newId]);
      }

      if (currentId > 0 && !isRightEdge) {
        const newId = currentId + 1 - width;
        handleClickSquare(squares[newId]);
      }

      if (currentId > 10) {
        const newId = currentId - width;
        handleClickSquare(squares[newId]);
      }

      if (currentId > 11 && !isLeftEdge) {
        const newId = currentId - 1 - width;
        handleClickSquare(squares[newId]);
      }

      if (currentId < 98 && !isRightEdge) {
        const newId = currentId + 1;
        handleClickSquare(squares[newId]);
      }

      if (currentId < 90 && !isLeftEdge) {
        const newId = currentId - 1 + width;
        handleClickSquare(squares[newId]);
      }

      if (currentId < 88 && !isRightEdge) {
        const newId = currentId + 1 + width;
        handleClickSquare(squares[newId]);
      }

      if (currentId < 89) {
        const newId = currentId + width;
        handleClickSquare(squares[newId]);
      }
    }, 10);
  };

  const addFlag = (square) => {
    if (isGameOver) return;
    if (!square.checked && flags < bombAmount) {
      if (!square.flagged) {
        squares[square.id].flagged = true;
        flags++;
      } else {
        squares[square.id].flagged = false;
        flags--;
      }
    }
    checkForWin();
  };

  const gameOver = (square) => {
    squares[square.id].checked = true;
    isGameOver = true;
    squares.forEach((eachSquare) => {
      if (eachSquare.status === "bomb") {
        eachSquare.checked = true;
        eachSquare.flagged = false;
      }
    });
  };

  const checkForWin = () => {
    let matches = 0;
    console.log("clheck");
    for (let i = 0; i < squares.length; i++) {
      if (squares[i].flagged && squares[i].status === "bomb") matches++;
      if (matches === bombAmount) {
        console.log("You win");
        isGameOver = true;
        isGameWon = true;
      }
    }
  };

  const getNumberClass = (total) => {
    switch (total) {
      case 1:
        return "one";
      case 2:
        return "two";
      case 3:
        return "three";
      case 4:
        return "four";
      case 5:
        return "five";
      default:
        return "";
    }
  };

  const resetGame = () => {
    (squares = []),
      (width = 10),
      (bombAmount = 20),
      (flags = 0),
      (isGameWon = false),
      (isGameOver = false);
    createBoard();
  };
</script>

<style>
  .container {
    display: inline-block;
  }

  .grid {
    height: 600px;
    width: 600px;
    display: flex;
    flex-wrap: wrap;
    background-color: #cdcdcd;
    border: 10px dashed #666666;
  }

  .grid div {
    height: 60px;
    width: 60px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .valid {
    box-sizing: border-box;
    border: 5px outset #666666;
  }

  .bomb {
    /* background-color: #ff3e00; */
    box-sizing: border-box;
    border: 5px outset #666666;
  }

  .checked {
    background-color: black;
    border: 5px inset #666666;
  }

  .content {
    font-size: 28px;
    font-weight: 600;
  }

  .one {
    color: blue;
  }

  .two {
    color: green;
  }

  .three {
    color: yellow;
  }

  .four {
    color: purple;
  }

  .five {
    color: red;
  }

  h2 {
    text-transform: uppercase;
    font-size: 2em;
    font-weight: 100;
    color: #ff3e00;
  }

  .reset {
    display: inline;
    border: 2px solid red;
    padding: 10px 10px;
    box-sizing: border-box;
    font-size: 16px;
    font-weight: 200;
    cursor: pointer;
    text-transform: uppercase;
  }

  .reset:hover {
    background-color: antiquewhite;
  }

  .menu {
    margin-top: 20px;
  }

  .flag-count {
    margin-top: 15px;
  }
</style>

<div class="container">
  <div>
    <div class="grid">
      {#each squares as square (square.id)}
        <div
          id={square.id}
          class={`${square.status} ${square.checked ? 'checked' : ''} ${getNumberClass(square.totalBombs)}`}
          on:click={() => handleClickSquare(square)}
          on:contextmenu={(e) => {
            e.preventDefault();
            addFlag(square);
          }}>
          {#if square.checked && square.totalBombs && square.totalBombs !== 0}
            <span class="content" transition:fade>{square.totalBombs}</span>
          {/if}
          {#if isGameOver && !isGameWon && square.status === 'bomb'}
            <span class="content " transition:fade>💣</span>
          {/if}
          {#if square.flagged}
            <span class="content" transition:fade>🚩</span>
          {/if}
        </div>
      {/each}
    </div>
    <div class="flag-count">
      <span>Flags: {flags}</span>
    </div>
  </div>
  <div class="menu">
    {#if isGameOver}
      {#if isGameWon}
        <h2 transition:fade>You Won!</h2>
      {:else}
        <h2 transition:fade>Game Over</h2>
      {/if}
    {/if}
    <div class="reset" on:click={resetGame}>Reset</div>
  </div>
</div>
