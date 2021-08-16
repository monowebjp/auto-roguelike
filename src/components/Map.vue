<template>
  <div class="map">
    <dl>
      <dt>縦</dt>
      <dd>{{ vertical }}</dd>
    </dl>
    <dl>
      <dt>横</dt>
      <dd>{{ horizontal }}</dd>
    </dl>
    <div class="row" :style="{width: chipSize * horizontal + 'px' }" v-for="(innerDungeons, index) in entireDungeons" :key="index">
      <div class="col wall" v-for="item in innerDungeons" :key="item.id"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Map',
  data () {
    return {
      chipSize: 16,
      vertical: 50,
      horizontal: 50,
      wall: 9
    }
  },
  computed: {
    entireDungeons () {
      let dungeons = []
      let id = 0
      for (let i = 0; i < this.vertical; i++) {
        dungeons[i] = []
        for (let j = 0; j < this.horizontal; j++) {
          dungeons[i][j] = {
            id: id,
            chip: this.wall
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
  background-image: url(/static/img/base.png);
}
.wall {
  background-position: -16px -112px;
}
</style>
