<script>
  import Board from "./Board/Board.svelte";
</script>

<style>
  main {
    text-align: center;
    align-content: center;
    padding: 1em;
    max-width: 500px;
    margin: 0 auto;
  }

  h1 {
    margin: 15px;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
    color: #ff3e00;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <h1>Minesweeper</h1>
  <Board />
</main>
