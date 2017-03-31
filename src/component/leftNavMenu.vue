<template>
            <!--router为真时 index代表路由路径-->
            <el-menu default-active="2" class="el-menu-vertical-demo" router @open="handleOpen"
                     @close="handleClose" style="background: white">
                <el-menu-item index="userList"><i class="el-icon-menu"></i>
                    用戶管理
                </el-menu-item>
                <el-submenu index="1">
                    <template slot="title"><i class="el-icon-more"></i>服務器管理</template>
                    <el-menu-item-group>
                        <template slot="title">服務器管理</template>
                        <el-menu-item index="serviceAdd">新增</el-menu-item>
                        <el-menu-item index="serviceList">查看</el-menu-item>
                    </el-menu-item-group>
                    <el-menu-item-group title="服務器維護">
                        <el-menu-item index="1-3">选项3</el-menu-item>
                    </el-menu-item-group>
                    <el-menu-item-group title="服務器申請">
                        <el-menu-item index="serviceApply">新增</el-menu-item>
                        <el-menu-item index="serviceApplyList">查看</el-menu-item>
                    </el-menu-item-group>
                    <!--<el-submenu index="1-4">
                        <template slot="title">选项4</template>
                        <el-menu-item index="1-4-1">选项1</el-menu-item>
                    </el-submenu>-->
                </el-submenu>
                <el-menu-item index="home"><i class="el-icon-menu"></i>
                    table表格
                </el-menu-item>
                <el-menu-item index="form"><i class="el-icon-setting"></i>
                    form表单
                </el-menu-item>
                <el-menu-item index="progress">进度条</el-menu-item>
            </el-menu>

</template>


<script>
    export default{
        methods:{
            handleOpen(key, keyPath) {
                console.log(key, keyPath);
            },
            handleClose(key, keyPath) {
                console.log(key, keyPath);
            },
        }
    }
</script>