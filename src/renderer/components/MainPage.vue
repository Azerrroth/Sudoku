<template>
  <div id="outer">
    <main id="displays">
      <div class="number-side">
        <table class="number-part">
          <tr class="rows" v-for="count in 9" :key="count*10">
            <td class="num" v-for="countt in 9" :key="countt-1">
              <number :value.sync="numbers[count-1][countt-1]" @update:value="updateNum"></number>
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
          <el-button class="function-button" @click.native="Solve()"
            >求解</el-button><br />
          <el-button class="function-button" @click.native="Clean()"
            >清空</el-button
          ><br />
          <el-button class="function-button" id="random-build"  @click.native="Build()"
            >随机生成</el-button
          >
        </div>
        <div class="check">
          <i class="el-icon-check" id="correct" v-show="correct"></i>
          <i class="el-icon-close" id="error" v-show="!correct"></i>
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
      numbers: [[]],
      correct: false,
      delayTime: 0
    }
  },
  created () {
    for (let row = 0; row < 9; row++) {
      this.numbers[row] = []
      for (let column = 0; column < 9; column++) {
        this.numbers[row][column] = 0 // (column + row + 1) % 9
      }
    }
  },
  methods: {
    Clean () {
      // console.log(this.numbers)
      for (let row = 0; row < 9; row++) {
        for (let column = 0; column < 9; column++) {
          this.numbers[row][column] = 0
        }
      }
      this.correct = this.Check()
      this.$forceUpdate()
    },
    // equals (number) {
    //   return number === 45
    // },
    // check the numbers in block is on rule
    Check () {
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
          osums[Math.floor(i / 3)][Math.floor(j / 3)] += this.numbers[i][j]
        }
      }
      for (let i = 0; i < 3; i++) {
        if (!osums[i].every(equals)) return false
      }
      return rows.every(equals) && columns.every(equals)
    },
    Build () {
      // console.log(this.Check())
      if (this.Check()) {
        alert('You Win!')
      } else {
        alert('You Lose!')
      }
    },
    FindZero () {
      let count = 0
      let posList = []
      for (let i = 0; i < 9; i++) {
        for (let j = 0; j < 9; j++) {
          if (this.numbers[i][j] === 0) {
            let posXY = [i, j]
            posList.push(posXY)
            count++
          }
        }
      }
      // console.log(count)
      return [count, posList]
    },
    // Compute the list of zero block
    ComputeCandidate (zero) {
      let CandidateList = []
      let count = zero[0]
      let posList = zero[1]
      for (let i = 0; i < count; i++) {
        let Num = [1, 2, 3, 4, 5, 6, 7, 8, 9]
        let x = posList[i][0]
        let y = posList[i][1]
        let x0 = Math.floor(x / 3)
        let y0 = Math.floor(y / 3)
        for (let j = 0; j < 9; j++) {
          if (Num.indexOf(this.numbers[x][j]) !== -1) {
            Num.splice(Num.indexOf(this.numbers[x][j]), 1)
          }
        }
        for (let k = 0; k < 9; k++) {
          if (Num.indexOf(this.numbers[k][y]) !== -1) {
            Num.splice(Num.indexOf(this.numbers[k][y]), 1)
          }
        }
        for (let m = 0; m < 3; m++) {
          for (let n = 0; n < 3; n++) {
            if (Num.indexOf(this.numbers[x0 * 3 + m][y0 * 3 + n]) !== -1) {
              Num.splice(Num.indexOf(this.numbers[x0 * 3 + m][y0 * 3 + n]), 1)
            }
          }
        }
        CandidateList.push(Num)
      }
      return [CandidateList, posList, count]
    },
    // 找到可填写数字（Candidate）个数最少的位置，返回 数量、序号、位置
    FindMin (maps) {
      let NumOfCandidate = []
      let CandidateList = maps[0]
      let posList = maps[1]
      let count = maps[2]
      for (let i = 0; i < count; i++) {
        let temp = CandidateList[i].length
        NumOfCandidate.push(temp)
      }
      let Min = Math.min.apply(null, NumOfCandidate)
      let MinIndex = NumOfCandidate.indexOf(Min)
      return [Min, MinIndex, posList[MinIndex]]
    },
    async Solve () {
      let temp = this.FindZero()
      let posList = temp[1]
      let count = temp[0]
      let TotalTimes = 0
      // 侯选列表
      let PopCandidateList = []
      let PopPosList = []
      while (count) {
        let CandidateList = this.ComputeCandidate([count, posList])[0]
        temp = this.FindMin([CandidateList, posList, count])
        let Min = temp[0]
        let MinIndex = temp[1]
        let MinPos = temp[2]
        // 出现有为 0 （未填入数字）的点，且该点没有可以取的值
        if (Min === 0) {
          // 回退N步，直到上一个加入Pop Candidate List中的点不只有一种取值可能
          while (PopCandidateList[PopCandidateList.length - 1].length === 0) {
            let x = PopPosList[PopPosList.length - 1][0]
            let y = PopPosList[PopPosList.length - 1][1]
            this.numbers[x][y] = 0
            this.$forceUpdate()
            await this.sleep(this.delayTime)
            // 将上一次迭代填入空格的数去除（设为0）
            count += 1
            posList.push(PopPosList[PopPosList.length - 1])
            PopPosList.splice(-1, 1)
            PopCandidateList.splice(-1, 1)
          }
          let x = PopPosList[PopPosList.length - 1][0]
          let y = PopPosList[PopPosList.length - 1][1]
          this.numbers[x][y] = PopCandidateList[PopCandidateList.length - 1][0] // 赋前一个侯选位置的可选值 0
          this.$forceUpdate()
          await this.sleep(this.delayTime)

          PopCandidateList[PopCandidateList.length - 1].splice(0, 1)
          TotalTimes = TotalTimes + 1
        } else {
          let x = MinPos[0]
          let y = MinPos[1]
          this.numbers[x][y] = CandidateList[MinIndex][0] // 取出Min Candidate 所有可取值的第一个值
          this.$forceUpdate()
          await this.sleep(this.delayTime)
          let PopCandidate = CandidateList[MinIndex]
          CandidateList.splice(MinIndex, 1)
          PopCandidate.splice(0, 1)
          PopCandidateList.push(PopCandidate) // 将取出的可选数字最小的Candidate 放入Pop Candidate List中
          let PopPos = posList[MinIndex]
          posList.splice(MinIndex, 1)
          PopPosList.push(PopPos) // 将取出可选数字最小对应的点放入Pop Pos List 中
          count--
          TotalTimes++
        }
      }
      console.log(TotalTimes)
      this.correct = this.Check()
      return TotalTimes
    },
    sleep (ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    },
    updateNum () {
      this.correct = this.Check()
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
  visibility: hidden;
}
.signature {
  margin-top: 65px;
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
.check {
  margin-top: 74px;
  margin-left: 52px;
}
#error {
  font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB",
    "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
  font-size: 40px;
  color: rgb(245, 108, 108);
}
#correct {
  font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB",
    "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
  font-size: 40px;
  color: rgb(103, 194, 58);
}
</style>
