<template>
  <div>
    <div>
      <el-row :gutter="20" class="row1">
        <el-col :span="7">
          <el-card shadow="hover" style="width: 100%; height: 100%">
            <div slot="header" class="clearfix" style="background: antiquewhite">
              <span>Total Number of Issues</span>
            </div>
            <div style="font-size: 18px;text-align: center;">{{ this.open + this.close }}</div>
          </el-card>
        </el-col>
        <el-col :span="7">
          <el-card shadow="hover" style="width: 100%;">
            <div slot="header" class="clearfix" style="background: antiquewhite">
              <span>Opened Issues</span>
            </div>
            <div style="font-size: 18px;text-align: center;">{{ this.open }}</div>
          </el-card>
        </el-col>
        <el-col :span="7">
          <el-card shadow="hover" style="width: 100%;">
            <div slot="header" class="clearfix" style="background: antiquewhite">
              <span>Closed Issues</span>
            </div>
            <div style="font-size: 18px;text-align: center;">{{ this.close }}</div>
          </el-card>
        </el-col>
      </el-row>

      <el-row :gutter="20" class="row2">
        <el-col :span="7">
          <el-card shadow="hover" style="width: 100%; height: 100%;">
            <div slot="header" class="clearfix" style="background: antiquewhite">
              <span>Average</span>
            </div>
            <div style="font-size: 18px;text-align: center;">
              <!--              {{ this.average }} ms-->
              <!--              <br>-->
              {{ (this.average / 3600 / 1000).toString().substring(0,5) }} h
            </div>
          </el-card>
        </el-col>
        <el-col :span="7">
          <el-card shadow="hover" style="width: 100%;">
            <div slot="header" class="clearfix" style="background: antiquewhite">
              <span>Difference of extreme values</span>
            </div>
            <div style="font-size: 18px;text-align: center;">
              <!--              {{ this.difference }} ms-->
              <!--              <br>-->
              {{ (this.difference / 24 / 3600 / 1000).toString().substring(0,5) }} day
            </div>
          </el-card>
        </el-col>
        <el-col :span="7">
          <el-card shadow="hover" style="width: 100%;">
            <div slot="header" class="clearfix" style="background: antiquewhite">
              <span>variance</span>
            </div>
            <div style="font-size: 18px;text-align: center;">
              <!--              {{ this.variance}} ms-->
              <!--              <br>-->
              {{ (this.variance / 24 / 3600 / 1000).toString().substring(0,5) }} day
            </div>
          </el-card>
        </el-col>
      </el-row>
    </div>
        <div class="btn">
          <el-button type="primary" @click="drawBar()" style="max-height: 50px;margin-top: 200px" plain  >Percentage</el-button>
          <div id="mychart" style="width: 600px;height:400px;"></div>
        </div>
  </div>
</template>

<script>
// import char from '@/components/char'
// import bar from '@/components/bar'
import axios from 'axios'

export default {
  name: 'Issues',
  // components: { char },
  data () {
    return {
      total: '0',
      close: '1',
      open: '3',
      difference: '0.0',
      average: '0.0',
      variance: '0.0',
      barxData: [],
      baryData: []
    }
  },
  methods: {
    getData () {
      axios({
        method: 'GET',
        url: 'http://localhost:8080/repository/openissue?repos=' + this.$route.query.repo
      }).then(response => {
        // 须补充
        // this.total = response.data.data.
        this.open = response.data.data.quantity
        // this.open = response.data.data.
        // this.difference = response.data.data.
        // this.average = response.data.data.
        // this.variance = response.data.data.
        // this.barxData = response.data.data.
        // this.baryData = response.data.data.
      })
      axios({
        method: 'GET',
        url: 'http://localhost:8080/repository/closeissue?repos=' + this.$route.query.repo
      }).then(response => {
        // 须补充
        // this.total = response.data.data.
        this.close = response.data.data.quantity
        this.difference = response.data.data.range
        this.average = response.data.data.average
        this.variance = response.data.data.standardDeviation
        // this.barxData = response.data.data.
        // this.baryData = response.data.data.
      })
      this.total = this.open + this.close
      console.log(this.average)
      console.log(this.total)
    },
    drawBar () {
      console.log('draw')
      this.initEcharts()
      // axios({
      //   method: 'GET',
      //   url: 'http://localhost:8080/repository/openissue?repos=' + this.$route.query.repo
      // }).then(response => {
      //   // 须补充
      //   // this.barxData = response.data.data.
      //   // this.baryData = response.data.data.
      // })
      // this.barxData = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
      // this.baryData = [120, 200, 150, 80, 70, 110, 130]
      // setTimeout(() => {
      //   this.echartsInit() // 初始化品质异常单饼图
      // }, 100)
    },
    initEcharts () {
      // 饼图
      const option = {
        legend: {
          // 图例
          data: this.pieName,
          right: '5%',
          top: '35%',
          orient: 'vertical'
        },
        // title: {
        //   // 设置饼图标题，位置设为顶部居中
        //   text: '国内院士前五省份图示',
        //   top: '0%',
        //   left: 'center'
        // },
        series: [
          {
            type: 'pie',
            label: {
              show: true,
              formatter: '{b} :{d}%' // b代表名称，c代表对应值，d代表百分比
            },
            radius: '70%', //饼图半径
            data: [{ value: this.open, name: 'Open issue' }, { value: this.close, name: 'Closed issue' }]
          }
        ]
      }
      console.log(this.seriesData)
      let optionFree = {
        xAxis: {},
        yAxis: {},
        series: [
          {
            data: this.seriesData,
            type: 'line',
            smooth: true
          }
        ]
      }
      this.myChart = this.$echarts.init(document.getElementById('mychart'))
      this.myChart.setOption(option)
    },
    echartsInit () {
      console.log('aaa')
      // 柱形图
      // 因为初始化echarts 的时候，需要指定的容器 id='main'
      this.$echarts.init(document.getElementById('main')).setOption({
        xAxis: {
          type: 'category',
          data: this.barxData
        },
        yAxis: {
          type: 'value'
        },
        series: [{
          data: this.baryData,
          type: 'bar',
          showBackground: true,
          backgroundStyle: {
            color: 'rgba(220, 220, 220, 0.8)'
          }
        }]
      })
    }
  },
  mounted () {
    this.getData()
  }
}
</script>

<style>
.like {
  cursor: pointer;
  font-size: 25px;
  display: inline-block;
}

.row1 {
  margin-top: 30px;
  margin-bottom: 30px;
}

.btn {
  display: flex;
  text-align: left;
  justify-content: center;
}
</style>
