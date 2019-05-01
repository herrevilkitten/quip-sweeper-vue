<template>
  <div>
    <quip-sweeper
      v-if="state === 'playing'"
      :height="height"
      :width="width"
      :board="board"
      @click="handleBoardClick"
    >
    </quip-sweeper>
    <div v-if="state === 'boom'">
      You triggered a mine!
    </div>
    <div v-if="state === 'won'">
      You won!
    </div>
  </div>
</template>

<script>
import QuipSweeper from "./quip-sweeper/QuipSweeper.vue";

const BOARD_CONFIG = {
  easy: {
    height: 9,
    width: 9,
    count: 10
  },
  medium: {
    height: 16,
    width: 16,
    count: 40
  },
  hard: {
    height: 24,
    width: 24,
    count: 99
  }
};

export default {
  components: {
    QuipSweeper
  },
  props: ["isCreation"],
  data: function() {
    return {
      height: 9,
      width: 9,
      board: [],
      state: "playing",
      difficulty: "easy"
    };
  },
  created: function() {
    quip.apps.updateToolbar({
      toolbarCommandIds: [quip.apps.DocumentMenuCommands.MENU_MAIN],
      highlightedCommandIds: [],
      menuCommands: [
        {
          id: quip.apps.DocumentMenuCommands.MENU_MAIN,
          subCommands: [
            "sweeper-easy",
            "sweeper-medium",
            "sweeper-hard",
            quip.apps.DocumentMenuCommands.SEPARATOR,
            "sweeper-restart"
          ]
        },
        {
          id: "sweeper-easy",
          label: "Easy",
          handler: () => {
            this.difficulty = "easy";
            this.resetBoard();
          }
        },
        {
          id: "sweeper-medium",
          label: "Medium",
          handler: () => {
            this.difficulty = "medium";
            this.resetBoard();
          }
        },
        {
          id: "sweeper-hard",
          label: "Hard",
          handler: () => {
            this.difficulty = "hard";
            this.resetBoard();
          }
        },
        {
          id: "sweeper-restart",
          label: "Restart",
          handler: () => {
            this.resetBoard();
          }
        }
      ]
    });

    this.resetBoard();
  },
  methods: {
    resetBoard: function() {
      this.height = BOARD_CONFIG[this.difficulty].height;
      this.width = BOARD_CONFIG[this.difficulty].width;
      let mineMax = BOARD_CONFIG[this.difficulty].count;

      this.board = new Array(this.height * this.width);
      this.board.fill("?");

      let mineCount = 0;
      while (true) {
        const index = Math.floor(Math.random() * (this.height * this.width));
        if (this.board[index] !== "X") {
          this.board[index] = "X";
          mineCount = mineCount + 1;
          if (mineCount >= mineMax) {
            break;
          }
        }
      }
      this.state = "playing";
    },
    handleBoardClick: function($event) {
      console.log($event);

      const cell = this.board[$event.index];

      switch (cell) {
        case "X":
          this.state = "boom";
          console.log("BOOM");
          break;
        case "?":
          let mineCount = 0;
          for (let i = $event.row - 1; i <= $event.row + 1; ++i) {
            if (i < 0 || i >= this.height) {
              continue;
            }
            for (let j = $event.col - 1; j <= $event.col + 1; ++j) {
              if (j < 0 || j >= this.width) {
                continue;
              }

              if (i == $event.row && j == $event.col) {
                continue;
              }
              if (this.board[i * this.height + j] === "X") {
                mineCount++;
              }
            }
          }
          console.log("Mine Count:", mineCount);
          this.board = Array.from(this.board);
          this.board[$event.index] = mineCount;
          if (mineCount === 0) {
            for (let i = $event.row - 1; i <= $event.row + 1; ++i) {
              if (i < 0 || i >= this.height) {
                continue;
              }
              for (let j = $event.col - 1; j <= $event.col + 1; ++j) {
                if (j < 0 || j >= this.width) {
                  continue;
                }

                if (i == $event.row && j == $event.col) {
                  continue;
                }
                console.log("Testing ", i, j);
                this.handleBoardClick({
                  row: i,
                  col: j,
                  index: i * this.width + j
                });
              }
            }
          }
          break;
      }

      if (!this.board.find(item => item === "?")) {
        this.state = "won";
        console.log("YOU WON!");
      }
    }
  }
};
</script>


<style scoped lang="less">
</style>

