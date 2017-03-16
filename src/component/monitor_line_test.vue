<template>
    <div class="monitorChartLine row">
        <linechart class="col-md-6" :datach="datach" :datech="datech" line_id="line_one" line_title="运行内存" axislab="M" ></linechart>
        <linechart class="col-md-6" :datach="datacpu" :datech="datech" line_id="line_two" line_title="CPU" axislab="%" ></linechart>
    </div>
</template>
<script>
    import echarts_line from './echarts_line.vue'
    import Vue from 'vue'
    export default {
        data(){
            return{
                intervalid:'',
                datech:[],
                datach:[],
                datacpu:[]
            }
        },
        props:['response','count'],
        mounted(){
            var self=this;
            var initflag=1,sort=4//初始化标志
            var count=self.count;
            self.intervalid=setInterval(function () {
                if(self.response.length>0){
                    if(initflag){//初始化赋值count条数据
                        for (var i = count-1; i >= 0; i--) {
                            var tem=self.response[0].body[i]["date"];
                            tem =tem.substr(8,tem.lenght);
                            self.datech.push(tem)
                            self.datach.push(parseFloat(self.response[0].body[i].Process['Working Set']._total)/1024/1024)
                            self.datacpu.push(parseFloat(self.response[0].body[i].Processor['% Processor time']._Total))
                       }
                        initflag=0
                        count=100
                    }
                    var tem=self.response[0].body[0]["date"];
                    tem =tem.substr(8,tem.lenght);
                    if(self.datech[sort]!=tem){ //去重
                        if(self.datech.length>=count){
                            self.datech.shift()
                            self.datach.shift()
                            self.datacpu.shift()
                            sort=count-2
                        }
                        sort++
                    self.$set(self.datech,sort,tem);//更新数据
                    self.$set(self.datach,sort,parseFloat(self.response[0].body[0].Process['Working Set']._total)/1024/1024);//genxindiyitiao
                    self.$set(self.datacpu,sort,parseFloat(self.response[0].body[0].Processor['% Processor time']._Total));//genxindiyitiao
                    }
                }
            },2000)

        },
        components:{'linechart':echarts_line},
        beforeDestroy(){
            console.log('intervalid0:'+this.intervalid)
            clearInterval(this.intervalid)
        },
    }
</script>