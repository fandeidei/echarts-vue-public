### 使用文档
### 逻辑:父组件只用传值给子组件，子组件负责逻辑初始化等…
### barline.vue封装了echarts的柱状图,折线图
### 在你的vue文件引入barline.vue
<template>
 <barEchartsdata :data="barEchartsdata"></barEchartsdata>
</template>  
 import barEchartsdata from './index'
  export default {
    components:{
      barEchartsdata
    },
 data(){
    return{
      //一般数据都是从后台接口获取,这里为了方便 在data里面初始化就好了
            //柱状图加折线图
      barEchartsdata:{
        xAxis:['集团','客户','KA','商用','海外','电商'],
        data:[
           {
            data:['5','50','5','20','5','120'],
            color:"#519EF6",
            type:"bar", //这个复用组件只支持柱状图和折线图 
            width:20,
            zlevel:10,
            name:"供应成本控制率",
            labelIsShow:false,
            yAxisIndex:0,
            },
            {
            data:['60','50','55','100','150','100'],
            color:"#519EF6",
            type:"line",
            width:20,
            zlevel:10,
            name:"供应成本控制率",
            labelIsShow:false,
            yAxisIndex:0,
            },
          ],
      }
    }
  }
</script>
#### 就可以显示柱状图或者折线图了
