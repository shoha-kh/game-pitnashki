<template>
  <div class="timer">
    <span style="margin-right: 15px">
      Game Status: {{ play ? 'start' : 'stop'}}
    </span>
    <span class="timer__value" style="margin-right: 15px">
      Timer: {{ time }}
    </span>
    <button @click="reloadGame">Restart Game</button>
  </div>
  <div class="pitnashki">
    <div class="pitnashki__wrapper">
      <template v-for="item in squaresData" :key="item">
        <span
          v-if="!item.active"
          class="pitnashki__item"
          :style="
            item.value
            ? { left: item.position.x + 'px', top: item.position.y + 'px'}
            : { left: item.position.x + 'px', top: item.position.y + 'px', background: '#a5a5a5'}
          "
        >
          {{ item.value }}
        </span>
        <span
          v-else
          @click="emptySquare(item)"
          class="pitnashki__item"
          :style="{ left: item.position.x + 'px', top: item.position.y + 'px', cursor: 'pointer' }"
        >
          {{ item.value }}
        </span>
      </template>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      time: 0,
      interval: null,
      play: false,
      squares: [
        { id: 1, value: 1, position: { x: 0, y: 0 }, enabled: [2, 5] },
        { id: 2, value: 2, position: { x: 100, y: 0 }, enabled: [1, 3, 6] },
        { id: 3, value: 3, position: { x: 200, y: 0 }, enabled: [2, 4, 7] },
        { id: 4, value: 4, position: { x: 300, y: 0 }, enabled: [3, 8] },
        { id: 5, value: 5, position: { x: 0, y: 100 }, enabled: [1, 6, 9] },
        { id: 6, value: 6, position: { x: 100, y: 100 }, enabled: [2, 5, 7, 10] },
        { id: 7, value: 7, position: { x: 200, y: 100 }, enabled: [3, 6, 8, 11] },
        { id: 8, value: 8, position: { x: 300, y: 100 }, enabled: [4, 7, 12] },
        { id: 9, value: 9, position: { x: 0, y: 200 }, enabled: [5, 10, 13] },
        { id: 10, value: 10, position: { x: 100, y: 200 }, enabled: [6, 9, 11, 14] },
        { id: 11, value: 11, position: { x: 200, y: 200 }, enabled: [7, 10, 12, 15] },
        { id: 12, value: 12, position: { x: 300, y: 200 }, enabled: [8, 11, 16] },
        { id: 13, value: 13, position: { x: 0, y: 300 }, enabled: [9, 14] },
        { id: 14, value: 14, position: { x: 100, y: 300 }, enabled: [10, 13, 15] },
        { id: 15, value: 15, position: { x: 200, y: 300 }, enabled: [11, 14, 16] },
        { id: 16, value: null, position: { x: 300, y: 300 }, enabled: [12, 15] },
      ],
    }
  },
  computed: {
    squaresData() {
      let data = this.randomicSquares()
      data.map( i => i.active = false)

      const emptySquareIdx = data.findIndex( i => i.value === null)

      data[emptySquareIdx].enabled.forEach(squareId => {
        data[squareId - 1].active = true
      })

      return data;
    },
  },
  methods: {

    randomicSquares() {
      if (this.play) return this.squares

      let arr = []
      for (let i=0;i<16;++i) arr[i]=i;
      function shuffle(array) {
        let tmp, current, top = array.length;
        if(top) while(--top) {
          current = Math.floor(Math.random() * (top + 1));
          tmp = array[current];
          array[current] = array[top];
          array[top] = tmp;
        }
        return array;
      }

      const nums = shuffle(arr);
      nums.forEach((n, i) => {
        if (n !== 0) this.squares[i].value = n
        else this.squares[i].value = null
      })

      return this.squares
    },

    emptySquare(square) {
      let data = this.squaresData
      square.enabled.forEach(squareId => {
        if (data[squareId - 1].value !== null)
          return
        
        data[squareId - 1].value = square.value
        data[square.id - 1].value = null
      })

      if (this.play) return
      this.gameTimer()
      this.play = true
    },

    reloadGame() {
      this.gameTimer('stop')
      this.time = 0
      this.play = false
      this.randomicSquares()
    },

    gameTimer(action) {
      if (action === 'stop') {
        clearInterval(this.interval)
        return
      }
      this.interval = setInterval(()=>(this.time += 1), 1000)
    }

  }
}
</script>

<style lang="sass">
.timer
  width: 400px
  padding-top: 3em
  margin: 0 auto
  margin-bottom: 30px
.pitnashki
  &__wrapper
    position: relative
    display: flex
    flex-wrap: wrap
    width: 400px
    height: 400px
    margin: 0 auto
    background: #333
  &__item
    position: absolute
    display: inline-flex
    justify-content: center
    align-items: center
    width: 96px
    height: 96px
    border: 2px solid #333
    border-radius: 6px
    background: #888
</style>
