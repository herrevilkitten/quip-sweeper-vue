<template>
  <div class="quip-sweeper-board">
    <div
      v-for="row in height"
      :key="row"
      class="quip-sweeper-row"
    >
      <div
        v-for="col in width"
        :key="col"
        class="quip-sweeper-cell"
        :class="cellClass(row, col)"
        @click="clickCell(row, col)"
      >
        {{cellIcon(row, col)}}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["height", "width", "board"],
  data() {
    return {};
  },
  computed: {},
  methods: {
    cellClass: function(row, col) {
      const index = (row - 1) * this.width + (col - 1);
      const cell = this.board[index];

      switch (cell) {
        case "X":
        case "?":
          return "cell-unknown";
        case "0":
        case 0:
          return "cell-number";
        default:
          return "";
      }
    },
    cellIcon: function(row, col) {
      const index = (row - 1) * this.width + (col - 1);
      const cell = this.board[index];

      switch (cell) {
        case "X":
        case "?":
          return "?";
        case "0":
        case 0:
          return " ";
        default:
          return cell;
      }
    },
    clickCell: function(row, col) {
      console.log("Click on", row, col);

      const index = (row - 1) * this.width + (col - 1);
      const cell = this.board[index];
      console.log("board", index, cell);

      this.$emit("click", { index: index, row: row - 1, col: col - 1 });
    }
  },
  watch: {},
  mounted() {},
  beforeDestroy() {}
};
</script>

<style lang="less" scoped>
.quip-sweeper-board {
  display: flex;
  flex-direction: column;

  .quip-sweeper-row {
    display: flex;
    flex-direction: row;
    .quip-sweeper-cell {
      width: 2rem;
      height: 2rem;
      font-size: 1.5em;
      border: 1px solid blue;
      cursor: pointer;
      text-align: center;
      vertical-align: middle;

      &.cell-unknown {
        color: lightgray;
      }

      &.cell-number {
        color: green;
      }
    }
    .quip-sweeper-cell:hover {
      background: gray;
    }
    .quip-sweeper-cell:active {
      background: yellow;
    }
  }
}
</style>
