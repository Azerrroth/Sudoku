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
          <el-button class="function-button"
            >求解</el-button><br />
          <el-button class="function-button" @click.native="Clean()"
            >清空</el-button
          ><br />
          <el-button class="function-button" id="random-build"  @click.native="Build()"
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
        this.numbers[row][column] = (column + row + 1) % 9
      }
    }
  },
  methods: {
    Clean () {
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
          osums[Math.floor(i / 3)][Math.floor[j / 3]] += this.numbers[i][j]
        }
      }
      for (let i = 0; i < 3; i++) {
        if (!osums[i].every(equals)) return false
      }
      return rows.every(equals) && columns.every(equals)
    },
    Build () {
      console.log(this.numbers)
    },
    FindZero () {
      let count = 0
      let posList = []
      for (let i = 0; i < 9; i++) {
        for (let j = 0; j < 9; j++) {
          if (this.numbers[i][j] === 0) {
            let posXY = [i, j]
            posList.push(posXY)
          }
        }
      }
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
    Solve () {
      let temp = this.FindZero()
      let posList = temp[1]
      let count = temp[0]
      let TotalTimes = 0
      let PopCandidateList = []
      let PopPosList = []
      while (count) {
        let CandidateList = this.ComputeCandidate([count, posList])
        temp = this.FindMin([CandidateList, posList, count])
        let Min = temp[0]
        let MinIndex = temp[1]
        let MinPos = temp[2]
        if (Min === 0) {
          while (PopCandidateList[PopCandidateList.length - 1] === 0) {
            let x = PopPosList[PopPosList.length - 1][0]
            let y = PopPosList[PopPosList.length - 1][1]
            this.numbers[x][y] = 0
            count += 1
            posList.push(PopPosList[PopPosList.length - 1])
            PopPosList.splice(-1, 1)
            PopCandidateList.splice(-1, 1)
          }
          let x = PopPosList[PopPosList.length - 1][0]
          let y = PopPosList[PopPosList.length - 1][1]
          this.numbers[x][y] = PopCandidateList[PopCandidateList.length - 1][0] // 赋前一个侯选位置的可选值 0
          PopCandidateList[PopCandidateList.length - 1].splice(0, 1)
          TotalTimes = TotalTimes + 1
        } else {
          let x = MinPos[0]
          let y = MinPos[1]
          this.numbers[x][y] = CandidateList[MinIndex][0]

          let PopCandidate = CandidateList[MinIndex]
          CandidateList.splice(MinIndex, 1)
          PopCandidate.splice(0, 1)
          PopCandidateList.push(PopCandidate)
          
          let PopPos = posList[MinIndex]
          posList.splice(MinIndex, 1)
          PopPosList.push(PopPos)
          count--
          TotalTimes++
        }
      }
      console.log(TotalTimes)
      return TotalTimes
    }
    /*
    def Solve_Sokudu(Sokudu):
    #计算需要填空的坐标及个数:坐标列表:Coord,个数:Count
    Coord,Count=Locat_Zero(Sokudu)
    TotalCount=0
    #弹出的候选数列表
    PopCandidaList=[]
    #弹出的坐标列表
    PopCoordList=[]
    while Count:
        #计算需要填空的候补列表:Candidalist
        CandidaList=Compute_Cadida(Coord,Count,Sokudu)
        #计算最小的候选数个数的位置
        Min,MinIndex,MinCoord=Find_Min(CandidaList,Coord,Count)
        if Min==0:
            while len(PopCandidaList[-1])==0:
                #上一个空格的坐标
                x=PopCoordList[-1][0]
                y=PopCoordList[-1][1]
                #还原当前节点值
                Sokudu[x][y]=0
                Count=Count+1
                Coord.append(PopCoordList[-1])
                #删除当前节点坐标及候选数列表
                del PopCoordList[-1]
                del PopCandidaList[-1]
            #更改上一个空格位置处的值
            x=PopCoordList[-1][0]
            y=PopCoordList[-1][1]
            Sokudu[x][y]=PopCandidaList[-1][0]
            del PopCandidaList[-1][0]
            TotalCount=TotalCount+1
        else:
            #更改MinCoord位置处空格的值
            x=MinCoord[0]
            y=MinCoord[1]
            Sokudu[x][y]=CandidaList[MinIndex][0]
            #取出MinCoord位置处的候选数删除候选数的
            第一项后放到弹出来的候选数列表中

            PopCandida=CandidaList.pop(MinIndex)
            del PopCandida[0]
            PopCandidaList.append(PopCandida)
            #取出MinCoord的位置放到弹出来的坐标列表中
            PopCoord=Coord.pop(MinIndex)
            PopCoordList.append(PopCoord)
            Count=Count-1
            TotalCount=TotalCount+1
    return Sokudu,TotalCount
    */
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
