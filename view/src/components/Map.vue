<template>
  <div class="map">
    <div class="row" :style="{width: chipSize * horizontal + 'px' }" v-for="(innerDungeons, index) in entireDungeons" :key="index">
      <div class="col wall" v-for="item in innerDungeons" :key="item.id">
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

    return dungeons
  }

  calcCoordinate () {
    const coordinate = {}
    coordinate.x = Math.floor(Math.random() * this.horizontal)
    coordinate.y = Math.floor(Math.random() * this.vertical)

    return coordinate
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
  background-position: -16px -112px;
}
</style>
