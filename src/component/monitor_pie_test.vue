<template>
    <div class="monitorChartLine">
        <div class="col-md-6">
            <div v-for="item in items" class="col-md-4">
                <piechart :datach="dataarr[item[0]]" :pie_id="item[1]" :pie_title="item[0]" ></piechart>
            </div>
        </div>
        <div class="col-md-6">
            <sysinfo :url="sysinfourl"></sysinfo>
        </div>
        <!--<piechart :datach="dataarr[items[0]]" pie_id="pie_c" pie_title="C"></piechart>
        <piechart :datach="datad" pie_id="pie_d" pie_title="D"></piechart>
        <piechart :datach="datahard" pie_id="pie_hard" pie_title="HarddiskVolume"></piechart>
        <piechart :datach="datatotal" pie_id="pie_total" pie_title="Totlal"></piechart>-->
    </div>
</template>
<script>
    import echarts_pie from './echarts_pie.vue'
    import monitor_sys_info from  './monitor_sys_info.vue'
    import Vue from 'vue'
    var arr=[];
    export default {
        data(){
            return{
                intervalid:'',
                dataarr:[{}],   //内存数据数组
                datasysinfo:[],
                items:[],
            }
        },
        props:['response','pie_title','sysinfourl'],
        mounted(){
            var self=this
            var initflag=1//初始化标志
            self.intervalid=setInterval(function () {
                if(self.response.length>0){ //异步请求延时response.length=0
                    var pid=0;
                        if(initflag){
                            for(var obj in self.response[0].body[0].LogicalDisk['% Free Space']){
                                var diskname=String(obj)
                                self.items.push([diskname,'pieid_'+pid.toString()]);//硬盘初始化
                                pid++
                            }
                            initflag=0
                            //初始化data数据
                            for(var i=0;i<self.items.length;i++){
                                var diskname=self.items[i][0]
                                self.dataarr[diskname]=[]
                                self.$set(self.dataarr[diskname],0,parseInt(self.response[0].body[0].LogicalDisk['% Free Space'][diskname]));//genxindiyitiao

                            }
                    }

                    for(var i=0;i<self.items.length;i++){
                        var diskname=self.items[i][0]
                        self.$set(self.dataarr[diskname],0,parseInt(self.response[0].body[0].LogicalDisk['% Free Space'][diskname]));//genxindiyitiao
                    }
                }
                    /*self.$set(self.datad,0,parseInt(response.body[0].LogicalDisk['% Free Space']['D:']));//genxindiyitiao
                    self.$set(self.datahard,0,parseInt(response.body[0].LogicalDisk['% Free Space'].HarddiskVolume1));//genxindiyitiao
                    self.$set(self.datatotal,0,parseInt(response.body[0].LogicalDisk['% Free Space']._Total));//genxindiyitiao
*
                //console.log('chucuo1:'+self.datach)
                /*addData(true);*/
            },3000);
        },
        components:{'piechart':echarts_pie,'sysinfo':monitor_sys_info},
        beforeDestroy(){
            console.log('intervalid0:'+this.intervalid)
            clearInterval(this.intervalid)
        },
    }
</script>