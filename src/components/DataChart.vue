<script setup lang="ts">
import 'echarts/lib/chart/line'
import { use } from 'echarts/core'
import type { LineSeriesOption } from 'echarts/charts'
import { LinesChart } from 'echarts/charts'
import {
  GridComponent,
} from 'echarts/components'
import { CanvasRenderer } from 'echarts/renderers'
import VChart, { THEME_KEY } from 'vue-echarts'
import {  provide, ref, watch } from 'vue'
import axios from 'axios'

interface Unemployment {
  country: string,
  code: string,
  2010: number,
  2011: number,
  2012: number,
  2013: number,
  2014: number,
}

use([
  CanvasRenderer,
  LinesChart,
  GridComponent,
])
provide(THEME_KEY, 'dark')

const data = ref<Unemployment[]>([]);
const mapData = ref<Unemployment[]>([]);

async function fetchData(){
  try{
    const response = await axios.get('http://localhost:8000/api/unemployment');
    for(const key in response.data.data){
      data.value.push(response.data.data[key]);
    }
    response.data.data.map((item:any)=>{
      mapData.value.push({
        name: item.country,
        type: 'line',
        data: [item['2010'], item['2011'], item['2012'], item['2013'], item['2014']]
      })
    })
    console.log(mapData.value);
  }catch (error){
    console.error(error);
  }
}


fetchData();
const chartOptions = ref<LineSeriesOption[]>({
  title:{
    text: 'Unemployment Rate'
  },
  series: mapData.value,
  xAxis:{
    type:'category',
    data:['2010','2011','2012','2013','2014']
  },
  yAxis:{
    type:'value',
    show:true,
    min:0,
    max:100,
    axisLabel:{
      formatter: '{value}%'
    }
  }
})


watch(data,()=>{
console.log(data.value);

})
</script>

<template>
  <v-chart class="chart" :option="chartOptions" :autocapitalize="true" />
</template>

<style scoped>
.chart {
  height:100vh;
  width: 100%;
}
</style>