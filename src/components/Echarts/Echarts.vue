<template>
  <div style="width: 500px;height: 300px;background:#fff" :id="id" :class="className"></div>
</template>
<script>
  import * as echarts from 'echarts';
  export default {
    props: ['options','id','className'],
    data: function () {
      return { }
    },
    mounted: function(){
      var myChart = echarts.init(document.getElementById(this.id));
      myChart.setOption(this.options);
    }
  }
</script>