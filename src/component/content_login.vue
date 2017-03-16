<template>
    <div>
    <!--顶部导航-->
    <div class="row head">
        <div class="col-md-12 headmenu">

        </div>
    </div>
    <!--分割线-->
    <!--<div class="split1" style="height:10px"></div>-->
    <div class="row center">
        <div class="col-md-3"></div>
        <div class="col-md-6"><Login></Login></div>
        <div class="col-md-3"></div>
    </div>
    <div class="footer"></div>
    </div>
</template>

<script>
    import Login from "./login.vue"
    export default{
        components: {"Login": Login},
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
