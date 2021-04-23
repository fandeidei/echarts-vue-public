<template>
    <div class="bar-line" :id="id"></div>
</template>

<script>
export default {
    components: {},
    props: {
        /* 数据 */
        //数据类型
        data:{
            type:Object
        },
        //标题
        title:{
            type:String,
            default:'柱状折线图'
        },
        //直角坐标系内绘图网格
        grid:{
            type:Object,
            default:()=>{
                return {
                    //左间距 右间距 底部距离 顶部距离
                    left: 50,
                    right: 10,
                    bottom: '20%',
                    top:35
                }
            }
        },
        //刻度标签旋转
        rotate:{
            type:Number,
            default:0
        },
        /* 图例设置 */
        legend:{
            type:Boolean,
            default:false
        },
        // 图例距离底部距离
        legendBottom: {
            type: Number,
            default: 2
        },
        //图例顶部距离
        legendTop: {
            type: Number
        },
        //鼠标移到图表上后展示的类似数据点详情的一个说明
        tooltipFormatter:{
            type:Object,
            default:()=>{
                return {
                    tooltipArray:[]
                }
            }
        },
        //图例输出文案
        legendFormatter:{
            type:Object,
        },
        //是否显示Y轴
        yAxisIsShow:{
            type:Boolean,
            default:true
        },
        //X轴的字体颜色
        xAxisAxisLabel:{
            type:Object,
            default:()=>{
                return {
                   show:true,
                   color:'#BBBABA',
                   fontSize:12,
                }
            }
        },
        //X轴的展示内容
        xAxisFormatterType:{
            type:String,
        },
        //X轴的底线颜色
        xAxisAxisLine:{
            type:Object,
            default:()=>{
                return {
                   show:true,
                   color:'#BBBABA'
                }
            }
        },
        //x轴刻度样式
        xAxisplitLine:{
            type:Object,
            default:()=>{
                return {
                   show:false,
                   type:'dotted',
                   color:'#000'
                }
            }
        },
        //y轴刻度样式
        yAxisplitLine:{
            type:Object,
            default:()=>{
                return {
                   show:true,
                   type:'dotted',
                   color:'rgba(255,255,255,0.15)'
                }
            }
        },
        //Y轴文字轴样式
        yAxisAxisLine:{
            type:Object,
            default:()=>{
                return {
                   show:false,
                   type:'dotted',
                   color:'#BCBCBC',
                }
            }
        },
        //Y轴文字
        yAxisAxisLabel:{
            type:Object,
            default:()=>{
                return {
                   show:true,
                   color:'rgba(255, 255, 255, .6)',
                   fontSize:12,
                   fontWeight:700
                }
            }
        },
        //Y轴刻度线
        yAxisAxisTick:{
            type:Boolean,
            default:false
        },
        //提示
        toolTipData:{
            type:Array,
            default:()=>{
                return [
                   {
                      name:'1月',
                      list:[
                          {name:'2018',value:'1,234万'},
                          {name:'2019',value:'2,222万'},
                          {name:'2020',value:'2,222万'},
                      ]
                  },
                  {
                      name:'2月',
                      list:[
                          {name:'2018',value:'1,234万'},
                          {name:'2019',value:'2,222万'},
                          {name:'2020',value:'2,222万'},
                      ]
                  },
                ]
            }
        },
        //点击事件
        chartHandleClick:{
            type:Function
        },
        agemom:{
            type:Boolean,
        },
        ySetNum:{
            type:Array,
            default:null
        },
        // 判断哪个模块进入 显示哪个模块的tip
        echartTypeModule:{
            type: String,
        }
    },
    data() {
        //初始化定义id mychart flag 属性
        return {
            id:'',
            myChart:'',
            flag:true,
        };
    },
    //创建周期后做的事情
    created() {
        //当前的id=生成的20位随机字符串
        this.id = this.genRandomString(20)
    },
    //载入周期后做的事情
    mounted() {
        //this赋值给_this,当前的id赋值给doc 然后初始化echarts
        var _this = this
        var doc = document.getElementById(this.id);
        var echarts =  _this.$echarts;
        _this.myChart = echarts.init(doc);
        window.addEventListener('resize',() => {
            _this.myChart.resize();
        });
        // _this.myChart.getZr().on('click',function(){
        //     let canvas =  document.querySelectorAll('.bar-line');
        //     for(let i=0;i<canvas.length;i++){
        //         if(canvas[i].getAttribute('id') == _this.id){
        //             _this.myChart.dispatchAction({
        //                 type: 'showTip'
        //             })
        //         }else{
        //             let myChart = echarts.init(canvas[i]);
        //             myChart.dispatchAction({
        //                 type: 'hideTip'
        //             })  
        //         }
                
            //}
        //})
        _this.barChartFun()
    },
    watch: {
        data: {
            handler(newval) {
                this.barChartFun()
            },
            deep: true
        }
    },
    computed: {},
    methods: {
        //处理
        barChartFun(){
            var _this = this
            //定义两个数组 seriesData color
            let seriesData = [] , color = [];
            console.log(111)
            console.log(_this.data.data)
            _this.data.data.map((item)=>{
                //遍历图例的颜色
                //如果当前传入的颜色=true color数组里面就添加item第一个数据的颜色
                //又如果item的颜色=黑色 不处理 否则color里面添加一个item的颜色
                // if(item.barGradientsTrue === true){
                //     color.push(item.barGradientsColor[0])
                // }else if(item.color == 'rgba(0,0,0,0)'){

                // }else{
                //     color.push(item.color)
                // }
                   
                //判断当前传过来的数据 高于80为蓝色 50-80为黄色 低于50为红色
                // alert(item.data)  
                // for(var i = 0;i<item.data.length;i++){
                //     if(Number(item.data[i]) > 80){
                //         color.push('rgba(0,0,255,0.3)')
                //     }else if(Number(item.data[i]) > 50){
                //         color.push('rgba(255,255,0,0.3)')
                //     }else{
                //         color.push('rgba(255,0,0,0.3)')
                //     }
                // } 
                // alert(color)

                //series的数据
                seriesData.push({
                    stack:item.stack, //名字一样就重叠
                    name:item.name,   //柱子名字
                    type:item.type,   //是柱子图还是折线图
                    data:item.data,   //数据
                    yAxisIndex: item.yAxisIndex , //Y轴index
                    barWidth:item.width, //柱子宽度
                    symbol:item.symbol == undefined ? 'emptyCircle' : item.symbol, //折线图拐点样式
                    barGap:item.barGap, //柱子间距
                    zlevel:item.zlevel, //柱子层级
                    showSymbol: true,  //是否显示点
                    symbolSize:item.symbolSize, //点的大小
                    smooth:false, //是否是波浪线 false 就是直角线
                    lineStyle: {
                        normal: {
                            color: item.color, // 线条颜色
                            type:item.typeLine,
                            opacity:0.6
                        },
                        // borderColor: '#f0f'
                    },
                    itemStyle:{
                        normal:{
                          color:function(params){
                                    let color 
                                    if(Number(params.data) >= 80){
                                          color =  'rgba(25, 167, 224)'
                                        //color.push('rgba(0,0,255,0.3)')
                                    }else if(Number(params.data) >= 50){
                                         color =  'rgba(252,168,55)'
                                        //color.push('rgba(255,255,0,0.3)')
                                    }else if(Number(params.data) <= 50){
                                            color =  'rgba(235,45,32)'
                                        //color.push('rgba(255,0,0,0.3)')
                                    }
                                    return color
                          },
                        //    borderColor: item.color,
                        //    borderWidth: 1,
                        },
                        //触摸后的高亮样式
                        emphasis: {
                                color: item.color//hover拐点颜色定义
                                //  color:'white' 
                        },
                    },
                    areaStyle: { //区域填充样式
                        normal: {
                            //线性渐变，前4个参数分别是x0,y0,x2,y2(范围0~1);相当于图形包围盒中的百分比。如果最后一个参数是‘true’，则该四个值是绝对像素位置。
                            color: new _this.$echarts.graphic.LinearGradient(0, 0, 0, 1, [{
                                    offset: 0,
                                    color: item.areaStyleShadow == true  ? item.areaStyleColor[0] : 'rgba(0,0,0,0)',
                                },
                                {
                                    offset: 1,
                                    color:  item.areaStyleShadow == true  ? item.areaStyleColor[1] : 'rgba(0,0,0,0)',
                                }
                            ], false),
                            // shadowColor: 'rgba(53,142,215, 0.9)', //阴影颜色、、=
                            // shadowBlur: 20 //shadowBlur设图形阴影的模糊大小。配合shadowColor,shadowOffsetX/Y, 设置图形的阴影效果。
                        }
                    },
                    //柱状图上的文字定义
                    label:{
                        normal:{
                            //是否显示
                            show:true,
                            formatter:function(value){
                            //     let val = ''
                            //     // val = _this.chartDataProcess(value.data,_this.data.data[0].unit[1]).num + _this.chartDataProcess(value.data,_this.data.data[0].unit[1]).unit
                            //    return val
                              let val = ''
                              val =  value.data + '%'
                              return val
                            },
                            //定位在哪里
                            position: 'top',
                            color: function(){

                            },
                            //字体样式 大写 顶部距离 字体粗细
                            fontFamily:'pingfang',
                            fontSize:12,
                            marginTop:5,
                            fontWeight:'bold'
                        }
                    }
                })
            })
            var option = {
                // title:{
                //      text :'图表',
                //     //  color:'#000',
                //      fontSize:10
                //     },
                //提示框内容
                tooltip:{
                    show:false,
                    //触发类型；轴触发，axis则鼠标hover到一条柱状图显示全部数据，item则鼠标hover到折线点显示相应数据，
                    trigger:'axis',
                    //坐标轴指示器,坐标轴触发有效
                    axisPointer:{ 
                        //默认为line,line直线，cross十字准星,shadow阴影
                        type:'cross',
                        z:10
                    },
                    //背景颜色
                    backgroundColor:'rgba(85,85,85,0.8)',
                    //文本样式
                    textStyle:{
                        color:'#fff',
                        fontSize:14,
                        align:'left',
                        fontFamily:'pingfang'
                    },
                    //属性即可限制提示在图表范围内。
                    confine:true,
                    //额外添加到浮层的css样式
                    extraCssText: 'box-shadow:0px 6px 18px 0px rgba(11,29,48,0.55)',
                    formatter:function(params){

                        var relVal = params[0].name+'<br/>';
                        // let data1 =[]
                        // //针对市场地位操作
                        // if(_this.echartTypeModule=='市场地位'){
                        //     params = params.sort((a,b)=>{
                        //         return b.value-a.value;
                        //     })
                        // }
                        // for (var k = 0;k< params.length; k++){
                        //     _this.data.data.map((item,index)=>{
                        //         if(item.name == params[k].seriesName){
                        //             data1.push(item)
                        //         }
                        //     })
                        // }
                        // for (var i = 0; i < params.length; i++) {
                        //     if(params[i].seriesName!=''){
                        //         if(_this.data.data[i].barGradientsTrue === true && params[i].marker.includes('[object Object]')){
                        //             let colorStart = params[i].color.colorStops[0].color
                        //             let colorEnd = params[i].color.colorStops[1].color
                        //             params[i].marker = params[i].marker.replace('background-color:[object Object]','background-image: linear-gradient('+colorStart+', '+colorEnd+')')
                        //         }
                        //         if(params[i].seriesName=='当月目标'){
                        //            params[i].marker = params[i].marker.replace('background-color:rgba(0,0,0,0)','background-color:rgba(22, 126, 230, 1)')
                        //         }else if(params[0].seriesName=="人均效能" && params[1].seriesName=="目标"){
                        //             params[1].marker = params[1].marker.replace('background-color:rgba(0,0,0,0)','background-color:#FF4040')
                        //         }else if(params[i].seriesName=="编制"){
                        //             params[i].marker = params[i].marker.replace('background-color:rgba(0,0,0,0)','background-color:#FF4040')
                        //         }
                        //         relVal += params[i].marker+params[i].seriesName+':'+_this.chartDataProcess(params[i].value,data1[i].unit[0]).num+_this.chartDataProcess(params[i].value,data1[i].unit[0]).unit+'</br>'  
                        //     }
                        // }
                        // //完善提示点功能
                        // if(_this.tooltipFormatter.tooltipArray.length>0){
                        //     relVal = '';
                        //     _this.tooltipFormatter.tooltipArray.map((item,index)=>{
                        //         if(params[0].dataIndex == index){
                        //             relVal = item;
                        //         }
                        //     })
                        // }
                        
                        return relVal;
                    },
                },
                //直角坐标系内绘图网格
                grid: _this.grid,
                //颜色
                color:color,
                //图例组件  展先了不同系列的标记
                legend: {
                    //show:true为显示 false为隐藏
                    show: true,
                    //离容器的距离:居中
                    left: 'left',
                    //图例项的icon
                     icon: "rect",
                    //图例项离底部的距离
                    // bottom: _this.legendBottom,
                    //图例项离顶部的距离
                    top:0,
                    //图例标记的图形宽度
                    itemWidth: 16,
                    //图例标记的图形高度
                    itemHeight: 8,
                    //图例的公用文本样式
                    textStyle: {
                        fontSize: 10,
                        color: 'rgba(151, 154, 159, 1)'
                    },
                },
                //直角坐标系的x轴
                xAxis: [
                    {
                    //坐标轴类型
                    type: 'category',
                    //坐标轴两边留白策略
                    boundaryGap: true,
                    //坐标轴在grid区域中的分割线
                    splitLine: {
                        //是否显示分隔线.默认数值轴显示,类目轴不显示
                        show: _this.xAxisplitLine.show,
                       
                        //所有属性
                        lineStyle: {
                            type:_this.xAxisplitLine.type,
                            color: _this.xAxisplitLine.color,
                        }
                    },
                    //坐标轴线相关设置
                    axisLine: { //坐标轴轴线相关设置。数学上的x轴
                        show: _this.xAxisAxisLine.show,
                        //坐标轴线线的颜色
                        lineStyle: {
                            //color: _this.xAxisAxisLine.color,
                            color:'#557A96'
                        },
                    },
                    axisLabel: { //坐标轴刻度标签的相关设置
                        //是否显示刻度
                        show:_this.xAxisAxisLabel.show,
                        //刻度标签旋转的角度
                        rotate:_this.rotate,
                        // rotate:60,
                        //刻度标签的文本样式
                        textStyle: {
                            color: '#BBBABA',
                            //color: _this.xAxisAxisLabel.color,
                            margin: 10,
                            fontFamily:'pingfang',
                            fontSize:_this.xAxisAxisLabel.fontSize,
                            // fontWeight:'bold'
                        },
                        formatter: function (value) {
                            if(_this.xAxisFormatterType == 'date_month'){
                                value =  value.substring(4,6) + '月';
                                if(value.length == 2 && value[0] == 0){
                                    value = value[1]
                                }
                                if(value.length == 3 && value[0] == 0 && value[2] == '月'){
                                    value = value[1] + value[2]
                                }
                            }
                            if(value.length == 2 && value[0] == 0){
                                value = value[1]
                            }
                            if(value.length == 3 && value[0] == 0 && value[2] == '月'){
                                value = value[1] + value[2]
                            }
                            return value.length > 3 ? value.substr(0,3) + "..." : value;
                        },
                    },
                    //这个属性是x轴的间隔点点是否显示
                    axisTick: {
                        show: false,
                    },
                    data:_this.data.xAxis,
                }],
                //y轴
                yAxis: [
                    {
                        show:true,
                        //坐标轴类型
                        type: 'value',
                        // show:_this.yAxisIsShow,
                        // max:_this.agemom == true ? 0.35 : null,
                        //y坐标轴刻度最大值
                        max:_this.ySetNum ? _this.ySetNum[1] : _this.agemom == true ? 0.35 : null,
                        // min:_this.agemom == true ? 0.2 : null,
                        //y坐标轴刻度最小值
                        min:_this.ySetNum ? _this.ySetNum[0] : _this.agemom == true ? 0.2 : null,
                        //坐标轴的分割段数
                        splitNumber:3,
                        splitLine: {
                            show: _this.yAxisplitLine.show,
                            lineStyle: {
                                type:_this.yAxisplitLine.type,
                                color: _this.yAxisplitLine.color,
                            }
                        },
                        axisLine: { //坐标轴轴线相关设置。数学上的x轴
                            //show: _this.yAxisAxisLine.show,
                            show:false,
                            lineStyle: {
                                // color: _this.yAxisAxisLine.color,
                                color:'#fff',
                                type: _this.yAxisAxisLine.type,
                            },
                        },
                        ////坐标轴刻度标签的相关设置
                        axisLabel: {
                            //刻度标签是否显示
                            // show:_this.yAxisAxisLabel.show,
                            show:true,
                            //刻度样式
                            textStyle: {
                                color: _this.yAxisAxisLabel.color,
                                fontFamily:'pingfang',
                                fontSize:_this.yAxisAxisLabel.fontSize,
                                // fontWeight:'bold'
                            },
                            formatter:function(value) {
                                // let val
                                // if(_this.data.data[0].unit[0]=='percent'){
                                //     // val = _this.dataReProcess(value,_this.data.data[0].unit[0]).num + _this.dataReProcess(value,_this.data.data[0].unit[0]).unit
                                // }else{
                                //     // val = _this.dataReProcess(value,_this.data.data[0].unit[0],_this.data.data[0].unit[1]).num
                                // }
                                return value + '%'
                            }
                        },
                        //这个属性是y轴的间隔点点是否显示
                        axisTick: {
                            show: _this.yAxisAxisTick,
                            inside:true
                        },
                    },
                    //第二条数据
                    {
                        type: 'value',
                        show:false,
                        // //坐标轴的分割段数
                        splitNumber:4,
                        splitLine: {
                            show: _this.yAxisplitLine.show,
                            lineStyle: {
                                type:_this.yAxisplitLine.type,
                                color: _this.yAxisplitLine.color,
                            }
                        },
                        axisLine: { //坐标轴轴线相关设置。数学上的x轴
                            show: _this.yAxisAxisLine.show,
                            lineStyle: {
                                color: _this.yAxisAxisLine.color,
                                type: _this.yAxisAxisLine.type,
                            },
                        },
                        //////坐标轴刻度标签的相关设置
                        axisLabel: {
                            show:_this.yAxisAxisLabel.show,
                            textStyle: {
                                color: _this.yAxisAxisLabel.color,
                                fontFamily:'pingfang',
                                fontSize:_this.yAxisAxisLabel.fontSize,
                                // fontWeight:'bold'
                            },
                        },
                        // //这个属性是y轴的间隔点点是否显示
                        axisTick: {
                            show: true,
                            inside:true
                        },
                    },
                ],
                //用户传过来的数据
                series:seriesData
            };
            _this.myChart.setOption(option);
        },
        //随机生成字符串的方法
        genRandomString(len) {
            const text = 'abcdefghijklmnopqrstuvwxyz0123456789';
            const rdmIndex = text => Math.random() * text.length | 0;
            let rdmString = '';
            for(; rdmString.length < len; rdmString += text.charAt(rdmIndex(text)));
            return rdmString;
        },
    },

};
</script>
<style lang="scss" scoped>
.bar-line{
    width: 100%;
    height: 100%;
}
</style>