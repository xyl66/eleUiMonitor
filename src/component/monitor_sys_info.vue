<template>
    <el-card class="box-card">
        <div class="systeminfo" slot="header"><span style="line-height:36px">系統信息</span></div>
        <div class="text item" v-for="info in sysinfodata">
            {{info[0]+info[1]}}
        </div>
    </el-card>
</template>
<script>
    export default{
        data () {
            return {
                sysinfodata:[]
            }
        },
        props:['url'],
        mounted(){
            var self=this
            this.$http.get(self.url).then(function(response){
                //huoqupanfugeshu
                self.sysinfodata.push(['CPU數量:',Object.keys(response.body.info['Win32_Processor']).length]);//genxindiyitiao
                self.sysinfodata.push(['內存:',((response.body.info['Win32_ComputerSystem'].ComputerSystem0.TotalPhysicalMemory)/1024/1024).toFixed(0)+'M']);//genxindiyitiao
                self.sysinfodata.push(['操作系統:',response.body.info['Win32_OperatingSystem'].OperatingSystem0.operation]);//genxindiyitiao
                self.sysinfodata.push(['64/32;',response.body.info['Win32_ComputerSystem'].ComputerSystem0.SystemType]);//genxindiyitiao
                self.sysinfodata.push(['硬盤：',((response.body.info['Win32_DiskDrive'].DiskDrive0.Size)/1024/1024/1024).toFixed(0)]);//genxindiyitiao
            },function (response) {
                console.log('chucuo0:'+response)
            });
        }
    }
</script>