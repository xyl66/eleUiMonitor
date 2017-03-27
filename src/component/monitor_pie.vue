<template>
    <div class="monitorChartLine row">
        <div v-for="item in items">
            <piechart :datach="dataarr[item[0]]" :pie_id="item[1]" :pie_title="item[0]"></piechart>
        </div>
    </div>
</template>
<script>
    import echarts_pie from './echarts_pie.vue'
    import Vue from 'vue'
    var arr=[];
    export default {
        data(){
            return{
                intervalid:'',
                dataarr:[{}],   //内存数据数组
                datad:[],
                datahard:[],
                datatotal:[],
                items:[],
            }
        },
        mounted(){
            var self=this;
            var url='http://10.134.159.91:8080/MSAPI/rest/readData/testGet/10.134.159.94'+"``"+'1';
            console.log('kaishi1')
            this.$http.get(url).then(function(response){
                //获取硬盘数
                var pid=0;
                for(var obj in response.body[0].LogicalDisk['% Free Space']){
                    console.log(obj)
                    var diskname=String(obj)
                    self.items.push([diskname,'pieid_'+pid.toString()]);//genxindiyitiao
                    pid++
                }
                console.log(self.items.length)
                for(var i=0;i<self.items.length;i++){
                    //硬盘初始化
                    var diskname=self.items[i][0]
                    self.dataarr[diskname]=[]
                    console.log(self.dataarr[String(self.items[i][0])])
                    self.$set(self.dataarr[diskname],0,parseInt(response.body[0].LogicalDisk['% Free Space'][diskname]));//genxindiyitiao

                }

            },function (response) {
                console.log('chucuo0:'+response)
            });

            self.intervalid=setInterval(function () {
                self.$http.get(url).then(response=>{
                    for(var i=0;i<self.items.length;i++){
                        var diskname=self.items[i][0]
                        self.$set(self.dataarr[diskname],0,parseInt(response.body[0].LogicalDisk['% Free Space'][diskname]));//genxindiyitiao
                    }
                    set(self.datatotal,0,parseInt(response.body[0].LogicalDisk['% Free Space']._Total));//genxindiyitiao
                })
            },3000);
        },
        components:{'piechart':echarts_pie},
        beforeDestroy(){
            console.log('intervalid0:'+this.intervalid)
            clearInterval(this.intervalid)
        },
    }
</script>