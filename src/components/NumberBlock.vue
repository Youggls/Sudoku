<template>
<div>
  <div v-for="line in lines" :key="line">
    <a-button
      :id="line+''+col"
      :type="typeArray[line][col]"
      style="width:42px;height:42px;"
      v-on:click="setCurrent(line, col)"
      v-on:blur="resetCurrent"
      :ghost="changeIsGhost(line, col)"
      v-show="fresh"
      v-for="col in cols"
      :key="col"
    >
    {{ getNumber(line, col) }}
    </a-button>
  </div>
</div>
</template>
<script>
export default {
  name: 'NumberBlock',
  data () {
    return {
      lines: [0, 1, 2, 3, 4, 5, 6, 7, 8],
      cols: [0, 1, 2, 3, 4, 5, 6, 7, 8],
      typeArray: [[]],
      currentLine: -1,
      currentCol: -1,
      fresh: true,
      notSet: 0,
      numbers: [[]],
      errLine: 1,
      errCol: 2,
      errBlock: 3,
      right: 0,
      rows: [[]],
      boxes: [[]],
      columns: [[]],
      isSloved: false,
      isGhost: true
    }
  },
  methods: {
    validate: function (line, col) {
      let i = Math.floor(line / 3)
      let j = Math.floor(col / 3)
      let arr = Array(9).fill(1)
      for (let r = 3 * i; r < 3 * (i + 1); r++) {
        for (let c = 3 * j; c < 3 * (j + 1); c++) {
          if (this.numbers[r][c] !== this.notSet) {
            if (arr[this.numbers[r][c]] !== 0) {
              arr[this.numbers[r][c]] = 0
            } else {
              this.changeType(r, c, this.errBlock)
              return this.errBlock
            }
          }
        }
      }
      arr.fill(1)
      for (let r = 0; r < 9; r++) {
        if (this.numbers[r][col] !== this.notSet) {
          if (arr[this.numbers[r][col]] !== 0) {
            arr[this.numbers[r][col]] = 0
          } else {
            this.changeType(r, col, this.errLine)
            return this.errLine
          }
        }
      }
      arr.fill(1)
      for (let c = 0; c < 9; c++) {
        if (this.numbers[line][c] !== this.notSet) {
          if (arr[this.numbers[line][c]] !== 0) {
            arr[this.numbers[line][c]] = 0
          } else {
            this.changeType(line, c, this.errCol)
            return this.errCol
          }
        }
      }
      this.changeType(line, col, this.right)
      return this.right
    },
    findAnswer: function () {
      this.dfsFindAnswer(0, 0)
      this.fresh = false
      this.fresh = true
    },
    placeNumber: function (number, row, col) {
      let boxIndex = Math.floor(row / 3) * 3 + Math.floor(col / 3)
      this.rows[row][number]++
      this.columns[col][number]++
      this.boxes[boxIndex][number]++
      this.numbers[row][col] = number
    },
    placeNextNumber: function (row, col) {
      if ((row === 8) && (col) === 8) {
        this.isSloved = true
      } else {
        if (col === 8) this.dfsFindAnswer(row + 1, 0)
        else this.dfsFindAnswer(row, col + 1)
      }
    },
    removeNumber: function (number, row, col) {
      let boxIndex = Math.floor(row / 3) * 3 + Math.floor(col / 3)
      this.rows[row][number]--
      this.columns[col][number]--
      this.boxes[boxIndex][number]--
      this.numbers[row][col] = this.notSet
    },
    couldPlace: function (number, row, col) {
      let boxIndex = Math.floor(row / 3) * 3 + Math.floor(col / 3)
      return this.boxes[boxIndex][number] + this.rows[row][number] + this.columns[col][number] === 0
    },
    dfsFindAnswer: function (row, col) {
      if (this.numbers[row][col] === this.notSet) {
        for (let num = 1; num < 10; num++) {
          if (this.couldPlace(num, row, col)) {
            this.placeNumber(num, row, col)
            this.placeNextNumber(row, col)
            if (!this.isSloved) {
              this.removeNumber(num, row, col)
            }
          }
        }
      } else this.placeNextNumber(row, col)
    },
    start: function () {
      let errNum = this.right
      for (let i = 0; i < 9; i++) {
        for (let j = 0; j < 9; j++) {
          errNum = this.validate(i, j)
          if (errNum !== this.right) {

          }
        }
      }
      for (let i = 0; i < 9; i++) {
        for (let j = 0; j < 9; j++) {
          if (this.numbers[i][j] !== this.notSet && this.validate(i, j) !== this.right) {
            return
          }
        }
      }
      this.findAnswer()
      this.fresh = false
      this.fresh = true
    },
    reset: function () {
      let vm = this
      vm.numbers = Array(9)
      vm.rows = Array(9)
      vm.columns = Array(9)
      vm.boxes = Array(9)
      vm.typeArray = Array(9)
      vm.isSloved = false
      vm.currentLine = -1
      vm.currentCol = -1
      for (let i = 0; i < 9; i++) {
        vm.numbers[i] = Array(9).fill(vm.notSet)
        vm.rows[i] = Array(10).fill(0)
        vm.columns[i] = Array(10).fill(0)
        vm.boxes[i] = Array(10).fill(0)
        vm.typeArray[i] = Array(9).fill('primary')
      }
      this.fresh = false
      this.fresh = true
    },
    getNumber: function (line, col) {
      if (this.numbers[line][col] === this.notSet) {
        return ''
      } else {
        return '' + this.numbers[line][col]
      }
    },
    setCurrent: function (line, col) {
      this.currentLine = line
      this.currentCol = col
    },
    resetCurrent: function () {
      this.currentLine = -1
      this.currentCol = -1
    },
    setNumber: function (number) {
      if (this.currentLine === -1 || this.currentCol === -1) {
        return false
      } else {
        this.placeNumber(number, this.currentLine, this.currentCol)
        this.validate(this.currentLine, this.currentCol)
        this.fresh = false
        this.fresh = true
        return true
      }
    },
    changeIsGhost: function (row, col) {
      return this.numbers[row][col] === this.notSet
    },
    changeType: function (row, col, type) {
      if (type === this.right) {
        this.$emit('disable', false)
        this.typeArray[row][col] = 'primary'
      } else {
        this.$emit('disable', true)
        this.typeArray[row][col] = 'danger'
      }
    }
  },
  mounted () {
    let vm = this
    vm.numbers = Array(9)
    vm.rows = Array(9)
    vm.columns = Array(9)
    vm.boxes = Array(9)
    vm.typeArray = Array(9)
    for (let i = 0; i < 9; i++) {
      vm.numbers[i] = Array(9).fill(vm.notSet)
      vm.rows[i] = Array(10).fill(0)
      vm.columns[i] = Array(10).fill(0)
      vm.boxes[i] = Array(10).fill(0)
      vm.typeArray[i] = Array(9).fill('primary')
    }
    document.onkeydown = function (event) {
      let e = event || window.event
      if (e && ((e.keyCode >= 49 && e.keyCode <= 57) || e.keyCode === 8)) {
        if (e.keyCode !== 8) vm.setNumber(e.keyCode - 48)
        else vm.setNumber(vm.notSet)
      }
    }
  }
}
</script>

<style scoped>
button {
  margin-right: 3px;
  margin-bottom: 3px
}
</style>
