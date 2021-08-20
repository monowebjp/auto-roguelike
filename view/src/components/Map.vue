<template>
  <div class="map">
    <div
      class="row"
      :style="{ width: chipSize * horizontal + 'px' }"
      v-for="row in entireDungeons"
      :key="row.id"
    >
      <div
        :class="{ col: true, wall: col.chip === 9 }"
        v-for="col in row.cols"
        :key="col.id"
      >
        {{ col.chip }}
      </div>
    </div>
  </div>
</template>

<script class="ts">
import { Options, Vue } from "vue-class-component";

@Options({
  props: {
    chipSize: Number,
    vertical: Number,
    horizontal: Number,
    wall: Number,
  },
})
export default class Map extends Vue {
  chipSize = 16;
  vertical = 30;
  horizontal = 30;
  wall = 9;
  row;

  /**
   * ダンジョン全体を生成します
   */
  get entireDungeons() {
    let dungeons = this.generateOutline;

    dungeons.forEach((row, rowIndex) => {
      row.id = "row-" + rowIndex;
      row.cols.forEach((col, colIndex) => {
        col.id = "col-" + colIndex;
      });
    });

    const startCoordinate = this.calcCoordinate();
    const goalCoordinate = this.calcCoordinate(startCoordinate);

    dungeons[startCoordinate.x].cols[startCoordinate.y].chip = 'S';

    dungeons[goalCoordinate.x].cols[goalCoordinate.y].chip = 'G';

    return dungeons;

    // return this.generateWay(dungeons, startCoordinate);
  }

  /**
   * ダンジョンの外枠を生成します
   */
  get generateOutline() {
    const verticalLength = this.vertical;
    const horizontalLength = this.horizontal;
    const dungeons = Array(horizontalLength);
    // const outlineWall = { chip: 9 };

    // const insideWall = { chip: 0 };

    // ダンジョン仕様
    // [
    //     {id: 0,
    //       cols: [ { id: 0, chip: 9 }, { id: 1, chip: 9 } ]
    //     }, {
    //       id: 1,
    //       cols: [ { id: 2, chip: 9 }, { id: 3, chip: 9 } ]
    //     }
    // ]

    // x軸
    for (let i = 0; i < horizontalLength; i++) {
      dungeons[i] = {};
      dungeons[i].cols = new Array(verticalLength).fill(null).map(() => ({ chip: 0 }));

      dungeons[i].cols[0] = { chip: 9 };
      dungeons[i].cols[verticalLength - 1] = { chip: 9 };
    }

    // y軸
    for (let i = 0; i < verticalLength; i++) {
      dungeons[0].cols[i] = { chip: 9 };
      dungeons[horizontalLength - 1].cols[i] = { chip: 9 };
    }

    return dungeons;
  }

  calcCoordinate(prevCoordinate = {x: 0, y: 0}) {
    const coordinate = {x: prevCoordinate.x, y: prevCoordinate.y};

    while (
      prevCoordinate.x === coordinate.x &&
      prevCoordinate.y === coordinate.y
    ) {
      coordinate.x = Math.floor(Math.random() * (this.horizontal - 2) + 1);
      coordinate.y = Math.floor(Math.random() * (this.vertical - 2) + 1);
    }

    return coordinate;
  }

  checkAdjacent(dungeons, x, y) {
    // 候補が2つあるかカウントする
    let cnt = 0;

    if (dungeons[x][y - 1] !== "W" && dungeons[x][y - 1] === "S") cnt++;

    if (dungeons[x][y + 1] === "9" || dungeons[x][y + 1] === "G") cnt++;

    if (dungeons[x - 1][y] === "9" || dungeons[x - 1][y] === "G") cnt++;

    if (dungeons[x + 1][y] === "9" || dungeons[x + 1][y] === "G") cnt++;

    return cnt > 1;
  }

  generateWay(dungeons, prevCoordinate) {
    const way = { x: prevCoordinate.x, y: prevCoordinate.y };
    let candidate = [];

    while (dungeons[way.x][way.y].chip !== "G") {
      // TODO:移動先の候補の条件を再考
      // 行き先のブロックはその先の候補を2箇所以上（上下左右のどれか、スタートと道になってる場所は除く）持つ
      // xの値が変わらなかったらyの値は変わる
      // xの値が変わったらyの値は変わらない

      // 候補を4つ挙げる
      // 上
      const isUpperCandidate = this.checkAdjacent(
        dungeons,
        prevCoordinate.x,
        prevCoordinate.y - 1
      );
      // 下
      dungeons[prevCoordinate.x][prevCoordinate.y + 1];
      // 左
      dungeons[prevCoordinate.x - 1][prevCoordinate.y];
      // 右
      dungeons[prevCoordinate.x + 1][prevCoordinate.y];

      candidate.push(prevCoordinate.x);
      if (prevCoordinate.x - 1 !== 0) candidate.push(prevCoordinate.x - 1);
      if (prevCoordinate.x + 1 !== this.horizontal - 1)
        candidate.push(prevCoordinate.x + 1);

      way.x = candidate[Math.floor(Math.random() * candidate.length)];

      candidate = [];

      if (prevCoordinate.x === way.x) {
        if (prevCoordinate.y - 1 !== 0) candidate.push(prevCoordinate.y - 1);
        if (prevCoordinate.y + 1 !== this.vertical - 1)
          candidate.push(prevCoordinate.y + 1);

        way.y = candidate[Math.floor(Math.random() * candidate.length)];
      } else {
        way.y = prevCoordinate.y;
      }

      // TODO:スタート地点、ゴール地点、すでに道になっているところは除く
      if (
        dungeons[way.x][way.y].chip !== "G" &&
        dungeons[way.x][way.y].chip !== "S"
      ) {
        dungeons[way.x][way.y].chip = "W";
        prevCoordinate.x = way.x;
        prevCoordinate.y = way.y;
      }
    }

    return dungeons;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.row {
  margin: 0 auto;
  display: flex;
}
.col {
  width: 16px;
  height: 16px;
  overflow: hidden;
  /*background-image: url(/static/img/base.png);*/
}
.wall {
  //background-position: -16px -112px;
  background-color: #000;
}
</style>
