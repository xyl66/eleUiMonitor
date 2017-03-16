<template>
    <div id="app1">
        <!--顶部导航-->
        <div class="row head">
            <div class="col-md-12 headmenu">
                <headNavMenu></headNavMenu>
            </div>
        </div>
        <!--分割线-->
        <!--<div class="split1" style="height:10px"></div>-->
        <div class="row center">
            <div class="col-md-2 leftmenu">
                <!--左侧导航-->
                <leftNavMenu></leftNavMenu>
            </div>
            <div class="col-md-10">
                <!--右侧视图-->
                <!-- 路由出口 -->
                <!-- 路由匹配到的组件将渲染在这里 -->
                <router-view></router-view>
            </div>
        </div>
        <div class="footer"></div>
    </div>
</template>

<script>
    import leftnavmenu from "./component/leftNavMenu.vue"
    import headnavmenu from "./component/headNavMenu.vue"
    export default{
        components: {"leftNavMenu": leftnavmenu, "headNavMenu": headnavmenu},
    }
</script>

<style>
    body {
        font-family: Helvetica, sans-serif;
    }
    .center{
        padding-top: 5px;
    }
</style>
