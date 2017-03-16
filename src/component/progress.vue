<template>
    <div class="progressbar">
        <el-progress :text-inside="true" :stroke-width="18" :percentage="0"></el-progress>
        <el-progress :text-inside="true" :stroke-width="18" :percentage="70"></el-progress>
        <el-progress :text-inside="true" :stroke-width="18" :percentage="100" status="success"></el-progress>
        <el-progress :text-inside="true" :stroke-width="18" :percentage="50" status="exception"></el-progress>
        <el-progress type="circle" :percentage="0"></el-progress>
        <el-progress type="circle" :percentage="25"></el-progress>
        <el-progress type="circle" :percentage="100" status="success"></el-progress>
        <el-progress type="circle" :percentage="50" status="exception"></el-progress>
        <p>methodtime:{{now()}}</p>
        <p>computedtime:{{cnow}}</p>
        <button @click="nowclick"></button>
        <button @click="cnowclick"></button>
        <button @click="change">change</button>
    </div>
</template>

<script>
    export default{
        data(){
            return{firstname:'good'}
        },
        computed:{
            cnow:function(){
                return this.firstname+Date.now()
            }
        },
        methods:{
            now:function(){
                return this.firstname+Date.now()
            },
            nowclick:function () {

                console.log(this.now())
            },
            cnowclick:function () {
                console.log(this.cnow)
            },
            change:function () {
                this.firstname='change'
            }
        }
    }
</script>