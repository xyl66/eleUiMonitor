<template>
    <div class="monitorChartLine row">
            <monitorPie :response="dataarr" count="5" :sysinfourl="sysinfourl"></monitorPie>
            <monitorLine :response="dataarr" count="5"></monitorLine>
    </div>
</template>
<script>
    import monitor_pie from './monitor_pie_test.vue'
    import monitor_line from './monitor_line_test.vue'
    import {monitorIp} from '../config'
    import Vue from 'vue'
    var arr=[];
    export default {
        data(){
            return{
                intervalid:'',
                dataarr:[],   //neicunsuzu
                datad:[],
                datahard:[],
                datatotal:[],
                items:[],
                sysinfourl:monitorIp+'MSAPI/rest/readData/getBasicData/10.134.159.94'
            }
        },
        mounted(){
            var self=this;
            var url=monitorIp+'MSAPI/rest/readData/testGet/10.134.159.94'+"``"+'5';
            this.$http.get(url).then(function(response){
                //huoqupanfugeshu
                self.$set(self.dataarr,0,response);//genxindiyitiao
            },function (response) {
                console.log('chucuo0:'+response)
            });
            self.intervalid=setInterval(function () {
                self.$http.get(url).then(response=>{
                    self.dataarr.shift()
                    self.$set(self.dataarr,0,response);//genxindiyitiao
                })
                //console.log('chucuo1:'+self.datach)
                /*addData(true);*/
            },2000);
        },
        components:{'monitorPie':monitor_pie,'monitorLine':monitor_line},
        beforeDestroy(){
            console.log('intervalid0:'+this.intervalid)
            clearInterval(this.intervalid)
        },
    }
</script>