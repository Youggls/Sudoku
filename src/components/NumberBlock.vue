<template>
<div>
  <div v-for="line in lines" :key="line">
    <a-button
      :id="line+''+col"
      type="primary"
      style="width:38px;height:38px;"
      v-on:click="setCurrent(line, col)"
      v-on:blur="resetCurrent"
      ghost
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
      currentLine: -1,
      currentCol: -1,
      fresh: true,
      numbers: [[]]
    }
  },
  methods: {
    getNumber: function (line, col) {
      if (this.numbers[line][col] === -1) {
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
        this.numbers[this.currentLine][this.currentCol] = number
        this.fresh = false
        this.fresh = true
        return true
      }
    }
  },
  mounted () {
    let vm = this
    vm.numbers = Array(9)
    for (let i = 0; i < 9; i++) {
      vm.numbers[i] = Array(9).fill(-1)
    }
    document.onkeydown = function (event) {
      let e = event || window.event
      if (e && (e.keyCode >= 49 && e.keyCode <= 57)) {
        vm.setNumber(e.keyCode - 48)
      }
    }
  }
}
</script>
