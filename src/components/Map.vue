<template>
  <div class="map">
    <div class="row" :style="{width: chipSize * horizontal + 'px' }" v-for="(innerDungeons, index) in entireDungeons" :key="index">
      <div class="col wall" v-for="item in innerDungeons" :key="item.id">
        {{item.chip}}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Map',
  data () {
    return {
      chipSize: 16,
      vertical: 30,
      horizontal: 30,
      wall: 9
    }
  },
  computed: {
    entireDungeons () {
      let dungeons = []
      let id = 0

      const startX = Math.floor(Math.random() * this.horizontal)
      const startY = Math.floor(Math.random() * this.vertical)

      const goalX = Math.floor(Math.random() * this.horizontal)
      const goalY = Math.floor(Math.random() * this.vertical)

      for (let i = 0; i < this.vertical; i++) {
        dungeons[i] = []
        for (let j = 0; j < this.horizontal; j++) {
          let chip = i === startX && j === startY ? 'S' : this.wall
          chip = i === goalX && j === goalY ? 'G' : chip
          dungeons[i][j] = {
            id: id,
            chip: chip
          }

          id++
        }
      }

      return dungeons
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
