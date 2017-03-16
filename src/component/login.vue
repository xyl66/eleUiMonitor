<template>
    <div class="content">
        <div class="center">
            <img src="../assets/logo.png">
        </div>
        <div class="center">
            <h1>{{ msg }}</h1>
        </div>
        <div class="row">
            <el-input v-model="user.uname" placeholder="请输入用户名"></el-input>
            <el-input type="password" v-model="user.pwd" placeholder="请输入密码"></el-input>
            <div class="center">
                    <el-button type="success" @click.native="startHacking">登录</el-button>
            </div>
        </div>
    </div>
</template>

<script>
    import Bus from '../bus.js'
    export default {
        data () {
            return {
                msg: 'Login Now!',
                tableData: [],
                user:{
                    uname:'',
                    pwd:''
                },
            }
        },

        mounted(){

        },
        methods: {
            startHacking () {
                this.$http.post('http://10.130.2.95/v1/user',{user:this.user},{
                    'headers':{

                    },emulateJSON:true}).then(function(response){
                        if(response.data.status===1){
                            alert(response.data.token);
                        setCookie('token',response.data.token,"h1");
                        setCookie('token1','goog',"h1");
                        alert(getCookie('token'));
                        Bus.$emit('is-login',1);
                        this.$router.push({path:'/home'});
                        }else {
                            alert(response.data.msg);
                        }
                    },function(response){
                        this.msg="erro"
                    }
                )
            },

        },

    }
</script>
<style>
    .center{
        text-align: center;
    }
</style>