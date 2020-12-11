<template>
  <div class="timer">
    <span style="margin-right: 15px">
      Game Status:
      <span v-if="play" style="color: green;">start</span>
      <span v-else style="color: red;">stop</span>
    </span>
    <span class="timer__value" style="margin-right: 15px">
      Timer: {{ time }}
    </span>
    <button @click="reloadGame">Stop and Restart Game</button>
  </div>
  <div class="pitnashki">
    <div class="pitnashki__wrapper">
      <template v-for="(item, index) in squares" :key="index">
        <span
          v-if="!item.active"
          class="pitnashki__item"
          :style="
            item.value
            ? { left: item.position.x + 'px', top: item.position.y + 'px', zIndex: '1'}
            : { left: item.position.x + 'px', top: item.position.y + 'px', background: '#a5a5a5', zIndex: '0'}
          "
        >
          {{ item.value }}
        </span>
        <span
          v-else
          @click="emptySquare(item.id)"
          class="pitnashki__item"
          :style="{ left: item.position.x + 'px', top: item.position.y + 'px', zIndex: '1', cursor: 'pointer' }"
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
  methods: {
    randomicNumbers(value) {
      let arr = []
      for (let i=0; i < value; ++i) arr[i] = i;
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
      return shuffle(arr);
    },

    squaresData() {
      this.squares.map( i => i.active = false)

      if (!this.play) {
        this.randomicNumbers(this.squares.length).forEach((n, i) => {
          if (n !== 0) this.squares[i].value = n
          else this.squares[i].value = null
        })
      }
      
      const emptySquareIdx = this.squares.findIndex(i => i.value === null)
      this.squares[emptySquareIdx].enabled.forEach(squareId => {
        const index = this.squares.findIndex(i => i.id === squareId)
        this.squares[index].active = true
      })
    },

    deleteSquareId(array, arrayId) {
      const arrIdx = array.findIndex(i => i == arrayId)
      if (!arrIdx) return
      array.splice(arrIdx, 1)
    },

    emptySquare(id) {
      const index = this.squares.findIndex(i => i.id === id)

      this.squares[index].enabled.forEach(squareId => {

        const enabledItemIdx = this.squares.findIndex(i => i.id === squareId)
        if (this.squares[enabledItemIdx].value !== null)
          return
        
        let copyCurrentSquare = { ...this.squares[index] }
        let copyEmptySquare = { ...this.squares[enabledItemIdx] }

        this.squares[enabledItemIdx].id = copyCurrentSquare.id
        this.squares[enabledItemIdx].position = copyCurrentSquare.position
        this.squares[enabledItemIdx].enabled = copyCurrentSquare.enabled

        this.squares[index].id = copyEmptySquare.id
        this.squares[index].position = copyEmptySquare.position
        this.squares[index].enabled = copyEmptySquare.enabled
      })

      if (!this.play) {
        this.play = true
        this.gameTimer()
      }
      this.squaresData()
    },

    reloadGame() {
      this.gameTimer('stop')
      this.time = 0
      this.play = false
      this.squaresData()
    },

    gameTimer(action) {
      if (action === 'stop') {
        clearInterval(this.interval)
        return
      }
      this.interval = setInterval(()=>(this.time += 1), 1000)
    }

  },
  created() {
    this.squaresData()
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
    transition: all .25s linear
    color: #eee
    font-size: 50px
</style>
