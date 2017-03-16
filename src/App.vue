<template>
    <div id="app1">
        <component v-bind:is="loginView"></component>
    </div>
</template>

<script>
    import login from "./component/content_login.vue"
    import contentmain from "./component/content_main.vue"
    import Bus from './bus.js'
    export default{
        data(){
            return{
                loginView:'Login'
            }
        },
        components: {"Login": login, "contentMain": contentmain},
        created(){
            var token=getCookie('token');
            if(token){
                this.loginView='contentMain';
            }
            var self=this;
            Bus.$on('is-login',function (id) {
                console.log("chufa");
                if(id){
                self.loginView='contentMain'
                }
            })
        },
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
