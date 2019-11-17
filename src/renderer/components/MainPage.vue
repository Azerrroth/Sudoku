<template>
  <div id="outer">
    <main id="displays">
      <div class="number-side">
        <table class="number-part">
          <tr class="rows" v-for="count in 9" :key="count*10">
            <td class="num" v-for="countt in 9" :key="countt-1">
              <number :value.sync="numbers[count-1][countt-1]"></number>
            </td>
          </tr>
        </table>
      </div>
      <div class="button-side">
        <div class="control-bar">
          <el-button
            class="control-button"
            icon="el-icon-minus"
            round
          ></el-button>
          <el-button
            class="control-button"
            icon="el-icon-close"
            round
          ></el-button>
        </div>
        <div class="h1-text-block">
          <h2 class="h1-text">Sudoku Solver</h2>
        </div>
        <div class="button-bar">
          <el-button class="function-button" @click.native="test()"
            >求解</el-button><br />
          <el-button class="function-button" @click.native="clean()"
            >清空</el-button
          ><br />
          <el-button class="function-button" id="random-build"
            >随机生成</el-button
          >
        </div>
        <div class="signature">
          <p id="signature-text">Design By Haozhe_Li</p>
        </div>
      </div>
    </main>
  </div>
</template>

<script scoped>
import Number from './MainPage/Number'

export default {
  name: 'main-page',
  components: { Number },
  data () {
    return {
      numbers: [[]]
    }
  },
  created () {
    for (let row = 0; row < 9; row++) {
      this.numbers[row] = []
      for (let column = 0; column < 9; column++) {
        this.numbers[row][column] = column + 1
      }
    }
  },
  methods: {
    clean () {
      console.log(this.numbers)
      for (let row = 0; row < 9; row++) {
        for (let column = 0; column < 9; column++) {
          this.numbers[row][column] = 0
        }
      }
      this.$forceUpdate()
    },
    // equals (number) {
    //   return number === 45
    // },
    // check the numbers in block is on rule
    check () {
      let rows = [0, 0, 0, 0, 0, 0, 0, 0, 0]
      let columns = [0, 0, 0, 0, 0, 0, 0, 0, 0]
      let osums = [
        [0, 0, 0],
        [0, 0, 0],
        [0, 0, 0]
      ]
      function equals (number) {
        return number === 45
      }
      for (let i = 0; i < 9; i++) {
        for (let j = 0; j < 9; j++) {
          rows[i] += this.numbers[i][j]
          columns[j] += this.numbers[i][j]
          osums[Math.floor(i / 3)][Math.floor[j / 3]] += this.numbers[i][j]
        }
      }
      for (let i = 0; i < 3; i++) {
        if (!osums[i].every(equals)) return false
      }
      return rows.every(equals) && columns.every(equals)
    },
    test () {
      console.log(this.numbers)
    }
  }
}
</script>

<style scoped>
#displays {
  display: flex;
}
.num {
  width: 50px;
  height: 50px;
  border: 0;
  margin: 0;
  padding: 0;
}
.number-side {
  width: 530px;
  height: 530px;
}
.number-part {
  margin: 30px;
  border-radius: 15px;
  background-color: rgb(236, 245, 255);
  /* box-shadow: 1px 1px 1000px rgb(188, 189, 185); */
}
.button-side {
  width: 210px;
  height: 530px;
}
.control-bar {
  display: inline-flex;
  margin-left: 118px;
}
.el-button.is-round {
  padding: 3.5px 15px;
}
.control-button {
  padding: 3.5px 15px;
  margin-left: 0;
  display: none;
}
.h1-text-block {
  margin-top: 47px;
}
.h1-text {
  font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB",
    "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  cursor: default;
}
.button-bar {
  display: block;
  margin-left: 30px;
  margin-top: 100px;
}
.function-button {
  padding: 12px 25px;
  margin-bottom: 10px;
  border-radius: 10px;
}
#random-build {
  padding: 12px 11px;
  margin-bottom: 0;
}
.signature {
  margin-top: 181px;
}
#signature-text {
  font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB",
    "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  cursor: default;
  font-size: 10px;
  color: rgb(218, 218, 218);
  text-align: right;
}
</style>
