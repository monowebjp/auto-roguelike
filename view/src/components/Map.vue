<template>
  <div class="map">
    <div class="row" :style="{width: chipSize * horizontal + 'px' }" v-for="(innerDungeons, index) in entireDungeons" :key="index">
      <div :class="{ col: true, wall: item.chip === 9 }" v-for="item in innerDungeons" :key="item.id">
        {{item.chip}}
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
  }
})

export default class Map extends Vue {
  chipSize = 16;
  vertical = 30;
  horizontal = 30;
  wall = 9;
  get entireDungeons () {
    let dungeons = []
    let id = 0

    const startCoordinate = this.calcCoordinate()
    const goalCoordinate = this.calcCoordinate()

    for (let i = 0; i < this.vertical; i++) {
      dungeons[i] = []
      for (let j = 0; j < this.horizontal; j++) {
        let chip = i === startCoordinate.x && j === startCoordinate.y ? 'S' : this.wall
        chip = i === goalCoordinate.x && j === goalCoordinate.y ? 'G' : chip
        dungeons[i][j] = {
          id: id,
          chip: chip
        }

        id++
      }
    }

    return this.generateWay(dungeons, startCoordinate);
  }

  calcCoordinate (startCoordinate = false) {
    const coordinate = {};

    while (startCoordinate.x === coordinate.x && startCoordinate.y === coordinate.y) {
      coordinate.x = Math.floor(Math.random() * (this.horizontal - 2) + 1);
      coordinate.y = Math.floor(Math.random() * (this.vertical - 2) + 1);
    }

    return coordinate
  }

  generateWay (dungeons, prevCoordinate) {
    const way = {x: prevCoordinate.x, y: prevCoordinate.y};
    let candidate = [];

    while (dungeons[way.x][way.y].chip !== "G") {
      // TODO:移動先の候補の条件を再考
      candidate.push(prevCoordinate.x);
      if (prevCoordinate.x - 1 !== 0) candidate.push(prevCoordinate.x - 1);
      if (prevCoordinate.x + 1 !== this.horizontal - 1) candidate.push(prevCoordinate.x + 1);

      way.x = candidate[Math.floor(Math.random() * candidate.length)];

      candidate = [];

      if(prevCoordinate.x === way.x) {
        if (prevCoordinate.y - 1 !== 0) candidate.push(prevCoordinate.y - 1);
        if (prevCoordinate.y + 1 !== this.vertical - 1) candidate.push(prevCoordinate.y + 1);

        way.y = candidate[Math.floor(Math.random() * candidate.length)];
      } else {
        way.y = prevCoordinate.y;
      }

      // TODO:スタート地点、ゴール地点、すでに道になっているところは除く
      if (dungeons[way.x][way.y].chip !== "G" && dungeons[way.x][way.y].chip !== "S") {
        dungeons[way.x][way.y].chip = 'W';
        prevCoordinate.x = way.x
        prevCoordinate.y = way.y
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
