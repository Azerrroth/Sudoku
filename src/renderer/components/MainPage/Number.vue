<template>
  <el-button 
    class="button" 
    type="primary" plain
    v-if="value === 0"
    @keyup.native="change($event)"
  > </el-button>
  <el-button
    class="button"
    type="primary" plain
    v-else
    @keyup.native="change($event)"
  >
    {{ value }}
  </el-button>
</template>
<script>
export default {
  name: 'number',
  props: {
    value: {
      type: Number,
      default: 0
    }
  },
  methods: {
    change (event) {
      if (event.keyCode === 27) {
        this.value = 0
      } else if (event.keyCode >= 49 && event.keyCode <= 57) {
        this.value = event.keyCode - 48
      } else if (event.keyCode >= 97 && event.keyCode <= 105) {
        this.value = event.keyCode - 96
      } else { return }
      this.$emit('update:value', 9)
      // this.$forceUpdate()
    }
  }
}
</script>

<style scoped>
.el-button{
  width: 50px;
  height: 50px;
  margin: 0;
  border-radius: 10px;
  font-size: 25px;
  /* box-shadow: 1px 1px 1px; */
}
</style>
