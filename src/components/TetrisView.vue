<template lang="pug">
.TetrisView(
  @keydown.left="move(-1,0)"
  @keydown.right="move(1,0)"
  @keydown.down="move(0,1)"
  @keydown.up="ground"
  @keydown.space="rotate"
  @keydown.enter="drop"
)
  .row(v-for="(row, y) in mergedField")
    FieldCell(
      v-for="(col, x) in row"
      v-if="y >= 1"
      @input="onInput($event, x, y)"
      ,:value="mergedField[y][x]"
      ,:key="`${y}-${x}`"
    )

  button(v-if="!isPlaying" @click="init") restart
</template>

<script>
import FieldCell from '@/components/FieldCell'

const tetromino = [
  [[0, 0, 0, 0], [1, 1, 1, 1], [0, 0, 0, 0], [0, 0, 0, 0]],
  [[2, 0, 0], [2, 2, 2], [0, 0, 0]],
  [[0, 0, 3], [3, 3, 3], [0, 0, 0]],
  [[4, 4], [4, 4]],
  [[0, 5, 5], [5, 5, 0]],
  [[0, 6, 0], [6, 6, 6], [0, 0, 0]],
  [[7, 7, 0], [0, 7, 7]]
]

let dropCounter = 0
let dropInterval = 1000
const rot = a => a[0].map((_, c) => a.map(r => r[c]).reverse())
export default {
  props: {
    width: { type: Number, default: 10 },
    height: { type: Number, default: 20 }
  },
  components: {
    FieldCell
  },
  data() {
    return {
      field: [],
      player: {
        x: 0,
        y: 0,
        mtx: []
      },
      isPlaying: false
    }
  },
  mounted() {
    this.init()
  },
  computed: {
    mergedField() {
      return this.field.map((row, y) => {
        return row.map((col, x) => this.playerAt(x, y) || col)
      })
    }
  },
  methods: {
    init() {
      this.field = [...Array(this.height)].map(row =>
        [...Array(this.width)].map(col => 0)
      )
      this.isPlaying = true
      this.update(0)
      this.createNext()
    },
    update(lastTimeValue) {
      const nowTimeValue = new Date().valueOf()
      dropCounter += nowTimeValue - lastTimeValue
      if (dropCounter > dropInterval) {
        dropCounter = 0
        this.drop()
      }
      if (this.isPlaying) {
        requestAnimationFrame(() => this.update(nowTimeValue))
      }
    },
    onInput(e, x, y) {
      this.$set(this.field[y], x, e)
    },
    createNext() {
      this.player = {
        x: Math.floor(this.width / 2) - 2,
        y: 0,
        mtx: tetromino[Math.floor(Math.random() * tetromino.length)]
      }
      this.isPlaying = this.isValid(this.player)
    },
    playerAt(x, y) {
      const row = this.player.mtx[-this.player.y + y]
      return row && row[-this.player.x + x]
    },
    drop() {
      const newPlayer = {
        ...this.player,
        y: this.player.y + 1
      }
      if (this.isValid(newPlayer)) {
        this.player = newPlayer
      } else {
        this.field = this.mergedField
        this.createNext()
      }
    },
    ground() {
      const newPlayer = this.player
      while (
        this.isValid({
          ...newPlayer,
          y: newPlayer.y + 1
        })
      ) {
        newPlayer.y += 1
      }
      this.player = newPlayer
    },
    move(x, y) {
      const newPlayer = {
        ...this.player,
        x: this.player.x + x,
        y: this.player.y + y
      }
      if (this.isValid(newPlayer)) {
        this.player = newPlayer
      }
    },
    rotate() {
      const newPlayer = {
        ...this.player,
        mtx: rot(this.player.mtx)
      }
      if (this.isValid(newPlayer)) {
        this.player = newPlayer
      }
    },
    isValid(player) {
      return player.mtx.every((row, y) => {
        return row.every((col, x) => {
          if (!col) return true
          const fRow = this.field[player.y + y]
          if (!fRow) return false
          const fCol = fRow[player.x + x]
          return !(fCol === undefined || fCol)
        })
      })
    }
  }
}
</script>

<style lang="stylus" scoped>

</style>
