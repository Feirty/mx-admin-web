<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-favor"></i> schart图表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="plugins-tips">
                vue-schart：vue.js封装sChart.js的图表组件。
                访问地址：<a href="https://github.com/lin-xin/vue-schart" target="_blank">vue-schart</a>
            </div>
            
        </div>
        <Echarts id="main" :options='options' class="echarts" style="width: 500px;height: 300px;top: 5px"></Echarts>
        <Echarts id="main2" :options='optionsLine' class="echarts" style="width: 500px;height: 300px;top: 5px;position: relative"></Echarts>
    </div>
</template>

<script>
  import * as echarts from 'echarts';
  import Echarts from '@/components/Echarts/Echarts.vue'; //组件

  export default {
    data: function () {
      return {
        options:{
          title: {
            text: '柱形图Bar'
          },
          tooltip: {},
          xAxis: {
            data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
          },
          yAxis: {},
          series: [{
            name: '销量',
            type: 'bar',
            data: [5, 20, 36, 10, 10, 20]
          }]
        },
        optionsLine: {
          title: {
            text: '线状图Line'
          },
          xAxis: {
            type: 'category',
            data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
          },
          yAxis: {
            type: 'value'
          },
          series: [{
            data: [820, 932, 901, 934, 1290, 1330, 1320],
            type: 'line'
          }]
        },
      }
    },
    components: {
      'Echarts': Echarts,
    }
  }
</script>

<style>
  .echarts{
    float: left;
  }
</style>