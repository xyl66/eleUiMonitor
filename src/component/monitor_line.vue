<template>
    <div class="monitorChartLine">
        <linechart :datach="datach" :datech="datech" line_id="line_one" line_title="neicun" axislab="M" count="5"></linechart>
    </div>
</template>
<script>
    import echarts_line from './echarts_line.vue'
    import Vue from 'vue'
    import {monitorIp} from '../config'
    var count=5;
    export default {
        data(){
            return{
                intervalid:'',
                datech:[],
                datach:[]
            }
        },
        mounted(){
            var self=this;
            var url=monitorIp+'MSAPI/rest/readData/testGet/10.134.159.94'+"``"+count;
            var url1=monitorIp+'MSAPI/rest/readData/testGet/10.134.159.94'+"``"+'1';
            console.log('kaishi1')
            this.$http.get(url).then(function(response){
                for (var i = count-1; i >= 0; i--) {
                    var tem=response.body[i]["date"];
                    tem =tem.substr(8,tem.lenght);
                    self.datech.push(tem)
                    self.datach.push(parseFloat(response.body[i].Process['Working Set']._total)/1024/1024)
                    /*self.$set(self.datech,i,tem);//genxindiyitiao
                    self.$set(self.datach,i,parseFloat(response.body[i].Process['Working Set']._total)/1024/1024);//genxindiyitiao
     */           }

            },function (response) {
                console.log('chucuo0:'+response)
            });
            self.intervalid=setInterval(function () {
                self.$http.get(url1).then(response=>{
                        var tem=response.body[0]["date"];
                        tem =tem.substr(8,tem.lenght);
                        self.datech.shift()
                        self.datach.shift()
                        self.$set(self.datech,count-1,tem);//genxindiyitiao
                        self.$set(self.datach,count-1,parseFloat(response.body[0].Process['Working Set']._total)/1024/1024);//genxindiyitiao
                })
                },3000);
        },
        components:{'linechart':echarts_line},
        beforeDestroy(){
            console.log('intervalid0:'+this.intervalid)
            clearInterval(this.intervalid)
        },
    }
</script>