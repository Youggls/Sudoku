<template>
<a-row>
  <a-col :span="24">
    <number-block v-on:disable="setIsDisableStart" ref="number"></number-block>
    <a-button :disabled="disableStart" v-show="fresh" v-on:click="start">开始求解</a-button>
    <a-button v-on:click="reset" type="danger">重设数独</a-button>
  </a-col>
</a-row>
</template>

<script>
import NumberBlock from './NumberBlock'
export default {
  name: 'HelloWorld',
  components: {
    NumberBlock
  },
  data () {
    return {
      disableStart: false,
      fresh: true
    }
  },
  methods: {
    start: function () {
      this.$refs.number.start()
    },
    reset: function () {
      this.$refs.number.reset()
    },
    setIsDisableStart: function (value) {
      this.disableStart = value
      this.fresh = false
      this.fresh = true
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
