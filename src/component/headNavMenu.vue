<template>
    <el-menu default-active="1" class="el-menu-demo" mode="horizontal" @select="handleSelect">
        <el-row class="headerbar success">
            <el-col :span="8" :offset="16">
                <el-menu-item index="1"><a :href="adminUrl">管理員中心</a></el-menu-item>

                <el-menu-item index="2">我的工作台</el-menu-item>

                <el-menu-item index="3">
                    <!-- 使用 router-link 组件来导航. -->
                    <!-- 通过传入 `to` 属性指定链接. -->
                    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
                    <router-link to="/foo">订单管理</router-link>
                </el-menu-item>
            </el-col>
        </el-row>
    </el-menu>
</template>

<script>
    import {serverAdminIp} from '../config'
    export default{
        data(){
            return{
                adminUrl:serverAdminIp+'admin'
            }
        },
        methods: {
            handleSelect(key, keyPath) {
                console.log(key, keyPath);
            }
        }
    }
</script>

<style>
    .headerbar{
        background-color: rgb(32,160,255);
    }
   .headerbar .el-menu-item,.headerbar .el-menu-item.is-active{
        color: #ffffff;
        opacity: 0.8;
    }
    .headerbar .el-menu-item.is-active{
        font-weight: 700;
        opacity: 1;
    }
</style>