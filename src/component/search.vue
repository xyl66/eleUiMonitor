<template>
    <div class="searchContent">
        <div class="block">
            <el-col :span="18">
            <el-input v-model="SN">
                <template slot="prepend">SN:</template>
            </el-input>
            </el-col>
            <el-col :span="6">
            <el-checkbox v-model="checked">精確</el-checkbox>
            </el-col>
            <el-col :span="18">
            <el-input v-model="num">
                <template slot="prepend">數量：</template>
            </el-input>
            </el-col>
            <el-col :span="18">
            <el-row type="flex" justify="start">
                <el-col :span="2">
                <span class="demonstration">時間範圍</span>
                </el-col>
                <el-col :span="8">
                <el-date-picker
                        v-model="value1"
                        type="datetime"
                        placeholder="选择日期时间">
                </el-date-picker>
                </el-col>
                <el-col class="line" :span="2">-</el-col>
                <el-col :span="8">
                <el-date-picker
                        v-model="value2"
                        type="datetime"
                        placeholder="选择日期时间"
                        align="right"
                        :picker-options="pickerOptions1">
                </el-date-picker>
                </el-col>
            </el-row>
            </el-col>
            <el-radio-group v-model="tem">
                <el-radio label="Processor">CPU</el-radio>
                <el-radio label="Process">MEMERY</el-radio>
                <el-radio label="LogicalDisk">disk</el-radio>
                <el-radio label="Network_Interface">network interface</el-radio>
            </el-radio-group>
            <el-button @click="searchechart">test</el-button>
        </div>
        <div id="echarts_content" style="width:600px;height: 400px"></div>
    </div>
</template>

<script>
    import Vue from 'vue'
    import {monitorIp} from '../config'
    /**
     * js时间对象的格式化;
     * eg:format="yyyy-MM-dd hh:mm:ss";
     */
    Date.prototype.format = function (format) {
        var o = {
            "M+": this.getMonth() + 1,  //month
            "d+": this.getDate(),     //day
            "h+": this.getHours(),    //hour
            "m+": this.getMinutes(),  //minute
            "s+": this.getSeconds(), //second
            "q+": Math.floor((this.getMonth() + 3) / 3),  //quarter
            "S": this.getMilliseconds() //millisecond
        }
        var week=["星期日","星期一","星期二","星期三","星期四","星期五","星期六"];
        if (/(y+)/.test(format)) {
            format = format.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
        }
        if (/(w+)/.test(format)){
            format = format.replace(RegExp.$1, week[this.getDay()]);
        }
        for (var k in o) {
            if (new RegExp("(" + k + ")").test(format)) {
                format = format.replace(RegExp.$1, RegExp.$1.length == 1 ? o[k] : ("00" + o[k]).substr(("" + o[k]).length));
            }
        }
        return format;
    }

    /**
     *js中更改日期
     * y年， m月， d日， h小时， n分钟，s秒
     */
    Date.prototype.add = function (part, value) {
        value *= 1;
        if (isNaN(value)) {
            value = 0;
        }
        switch (part) {
            case "y":
                this.setFullYear(this.getFullYear() + value);
                break;
            case "m":
                this.setMonth(this.getMonth() + value);
                break;
            case "d":
                this.setDate(this.getDate() + value);
                break;
            case "h":
                this.setHours(this.getHours() + value);
                break;
            case "n":
                this.setMinutes(this.getMinutes() + value);
                break;
            case "s":
                this.setSeconds(this.getSeconds() + value);
                break;
            default:

        }
    }
    //getMaxmin
    //maxmin=max or min
    function getMaxmin(arr,maxmin){
        if(maxmin=='max'){
            return Math.max.apply(Math,arr);
        }
        else if(maxmin=='min'){
            return Math.min.apply(Math,arr);
        }
    }
    ///
    var hideflag=1;
    var option = {
        title:{
            text: this.line_title,
            left: 'center',
            top: 20,
            textStyle: {
                color: '#000'
            }
        },
        tooltip:{
            trigger:'axis'
        },
        xAxis: {
            type: 'category',
            boundaryGap: false,
            data: [],
            axisLabel:{
                // 使用函数模板，函数参数分别为刻度数值（类目），刻度的索引
                formatter: function (value, index) {
                    // 格式化成月/日，只在第一个刻度显示年份
                    var y=value.substr(0,4);
                    var m=value.substr(4,2);
                    var d=value.substr(6,2);
                    var t=value.substr(8);
                    var date = new Date(value);
                    var texts = [m, d];
                    if (index === 0) {
                        texts.unshift(y);
                    }
                    return texts.join('/')+" "+t;
                }
            }
        },
        yAxis: {
            boundaryGap: [0, '50%'],
            type: 'value',
            splitNumber:5
        },
        dataZoom:[{
            type: 'inside',
            start: 0,
            end: 10,
        },{
            start:0,
            end:10,
            handleStyle:{
                color:'#fff',
                shadowBlur:3,
                shadowColor:'rgba(0,0,0,0.6)',
                shadowOffsetX:2,
                shadowOffsetY:2
            }
        }],
        series: [
            {
                name:'信息',
                type:'line',
                smooth:true,
                symbol: '',
                stack: 'a',
                itemStyle:{},
                areaStyle:{

                },
                data: []
            }
        ]
    };
    var myChart
    ///////////////////////
    /////////////////////////
    //////////////////////////
    export default {
        data() {
            return {
                SN:'',
                checked:true,
                num:'0',
                tem:'Processor',
                pickerOptions1: {
                    shortcuts: [{
                        text: '今天',
                        onClick(picker) {
                            picker.$emit('pick', new Date());
                        }
                    }, {
                        text: '昨天',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() - 3600 * 1000 * 24);
                            picker.$emit('pick', date);
                        }
                    }, {
                        text: '一周前',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', date);
                        }
                    }]
                },
                dateT: '',
                echartView:'Login',
                value1: '',
                value2: '',
                resultarr:[],
                datearr:[],
                yxisarr:[],
                //搜索結果關鍵字
            datakey:{
                Processor:
                    ["Processor","% Processor time","_Total"],
                Process:
                    ["Process","Working Set","_total"],
                LogicalDisk:
                    ["LogicalDisk","Free MegaBytes","_Total"],
                Network_Interface:
                    ["LogicalDisk","Free MegaBytes","_Total"]
            }
            };
        },
        mounted(){
            var echarts=require('echarts');
            myChart=echarts.init(document.getElementById("echarts_content"));
            myChart.setOption(option);
        },
        methods:{
            searchechart(){
                hideflag=1;
                var self=this;
                var d
                var d1=new Date(self.value1.format("yyyy/MM/dd hh:mm"))
                var d2=self.value2
                self.resultarr=[]
                self.datearr=[]
                self.yxisarr=[]
                console.log(d1.format("yyyy-MM-dd hh:mm"))
                d=new Date(d1.format("yyyy/MM/dd hh:mm"))
                //var url='http://10.134.159.91:8080/MSAPI/rest/readData/getOldData/'+self.SN+"``"+d1.format("yyyyMMdd hh:mm")
                d.add("h",1);
                console.log(d1.format("yyyy-MM-dd hh:mm"))
                console.log(d.format("yyyy-MM-dd hh:mm"))
                console.log(self.value1.format("yyyy-MM-dd hh:mm"))
                var strdend=d<d2?d.format("yyyyMMdd hh:mm"):d2.format("yyyyMMdd hh:mm")
                var url=monitorIp+'MSAPI/rest/readData/getOldData/'+self.SN+"``"+d1.format("yyyyMMdd hh:mm") +"``"+strdend+"``"+self.tem+"``"+self.num+"``"+"88";
                /*var datakey=[];
                switch(self.tem)
                {
                    case "Processor":
                        datakey=["Processor","% Processor time","_Total"]
                        breack
                    case "Process":
                        datakey=["Process","Working Set","_total"]
                        break
                    case "LogicalDisk":
                        datakey=["LogicalDisk","Free MegaBytes","_Total"]
                        break
                    case "Network_Interface":
                        datakey=["LogicalDisk","Free MegaBytes","_Total"]
                }*/
                myChart.showLoading()
                getdata(self,url,d,d2,self.resultarr,self.datearr,self.yxisarr)
            }
        }
    };
    var compare=function(prop) {
        return function (obj1,obj2) {
            var val1=obj1[prop],val2=obj2[prop];
            if(val1<val2){
                return -1;
            }else if(val1>val2){
                return 1;
            }else {
                return 0
            }
        }
    }
    function getdata(self,url,d1,d2,result,datearr,yxisarr){
        self.$http.get(url).then(function(response){
            if(hideflag){
                myChart.hideLoading()
                hideflag=0
            }
                //huoqupanfugeshu
                response.body.sort(compare("date"));
                result=result.concat(response.body)
                //console.log(result)
                var d=new Date(d1.format("yyyy/MM/dd hh:mm"))
                //var urlchange='http://10.134.159.91:8080/MSAPI/rest/readData/getOldData/'+self.SN+"``"+d1.format("yyyyMMdd hh:mm")
                d.add("h",1);
                var strdend=d<d2?d.format("yyyyMMdd hh:mm"):d2.format("yyyyMMdd hh:mm")
                if(d1<d2){
                    var urlchange=monitorIp+'MSAPI/rest/readData/getOldData/'+self.SN+"``"+d1.format("yyyyMMdd hh:mm") +"``"+strdend+"``"+self.tem+"``"+"testNum"+"``"+"testPage";
                    getdata(self,urlchange,d,d2,result,datearr,yxisarr)
                    }
                else {
                    //console.log(result)
                    var count=result.length
                    datearr=[]
                    yxisarr=[]
                    for(var i=0;i<count;i++){
                        datearr.push(result[i].date)
                        yxisarr.push(result[i].Processor["% Processor time"]._Total);//genxindiyitiao
                    }
                    console.log(datearr)
                    myChart.setOption({
                        xAxis: {
                            data: datearr
                        },
                        yAxis:{
                            //max:parseInt(getMaxmin(yxisarr,'max')) +10,
                            //min:parseInt(getMaxmin(yxisarr,'min'))-10
                        },
                        series: [{
                            name:'信息',
                            data: yxisarr
                        }]
                    });
                    }

            },function (response) {
                console.log('chucuo0:'+response)
            });
        //myChart.hideLoading()
        //console.log(result)
        var count=result.length
        datearr=[]
        yxisarr=[]
        var datakey=self.datakey[self.tem]
        for(var i=0;i<count;i++){
            datearr.push(result[i].date)
            yxisarr.push(result[i][datakey[0]][datakey[1]][datakey[2]]);//genxindiyitiao
        }
        myChart.setOption({
            xAxis: {
                data: datearr
            },
            yAxis:{
                //max:parseInt(getMaxmin(yxisarr,'max')) +10,
                //min:parseInt(getMaxmin(yxisarr,'min'))-10
            },
            series: [{
                name:'信息',
                data: yxisarr
            }]
        });

    }
</script>