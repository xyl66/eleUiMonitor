<template>
            <!--router为真时 index代表路由路径-->
            <el-menu default-active="2" class="el-menu-vertical-demo" router @open="handleOpen"
                     @close="handleClose" style="background: white">
                <el-submenu index="1">
                    <template slot="title">服務器管理</template>
                    <el-menu-item-group>
                        <template slot="title"><span class="glyphicon glyphicon-cog"></span>服務器管理</template>
                        <el-menu-item index="serviceAdd">新增</el-menu-item>
                        <el-menu-item index="serviceList">查看</el-menu-item>
                    </el-menu-item-group>
                    <el-menu-item-group title="服務器維護">
                        <el-menu-item index="1-3">选项3</el-menu-item>
                    </el-menu-item-group>
                    <el-menu-item-group>
                        <template slot="title"><span class="glyphicon glyphicon-plus-sign"></span>服務器申請</template>
                        <el-menu-item index="serviceApply">新增</el-menu-item>
                        <el-menu-item index="serviceApplyList">查看</el-menu-item>
                    </el-menu-item-group>
                    <!--<el-submenu index="1-4">
                        <template slot="title">选项4</template>
                        <el-menu-item index="1-4-1">选项1</el-menu-item>
                    </el-submenu>-->
                </el-submenu>
                <el-menu-item index="userList"><span class="glyphicon glyphicon-user"></span>用戶管理</el-menu-item>
                <el-menu-item index="form"><span class="glyphicon glyphicon-search"></span>系統搜索</el-menu-item>
                <el-menu-item index="progress"><span class="glyphicon glyphicon-dashboard"></span>監控窗口</el-menu-item>
                <el-menu-item index="home"><span class="glyphicon glyphicon-list"></span>測試頁面</el-menu-item>
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