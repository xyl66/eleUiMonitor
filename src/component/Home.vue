<template>
    <div class="content">
    <img src="../assets/logo.png">
    <h1>{{ msg }}</h1>
    <el-button type="success" @click.native="gettoken">Let's do it</el-button>
    <el-table :data="tableData" stripe style="width: 100%">
        <el-table-column
                md=8
                prop="date"
                label="日期"
                width="180">
        </el-table-column>
        <el-table-column
                md=8
                prop="name"
                label="姓名"
                width="180">
        </el-table-column>
        <el-table-column
                md=8
                prop="address"
                label="地址">
        </el-table-column>
    </el-table>
    </div>
</template>

<script>

import echarts_line from './echarts_line.vue'
    export default {
        data () {
            return {
                msg: 'Use Vue 2.0 Today!',
                tableData: []
            }
        },

        mounted(){
            var self=this;
            var url='http://10.130.2.95/getdate';
            this.$http.get(url).then(function(response){
                this.tableData=response.body.subjects

            },function (response) {
                console.log('chucuo:'+response)
            });
        },
        methods: {
            startHacking () {
                this.$notify({
                    title: 'It Works',
                    message: 'We have laid the groundwork for you. Now it\'s your time to build something epic!',
                    duration: 6000
                })
            },
            gettoken(){
                this.$http.get('http://10.130.2.95/indexapi').then(response=>{
                    console.log(response.body.request)
                    alert(response.data.msg);
                })
            }

        },
        components:{'linechart':echarts_line}
    }
</script>