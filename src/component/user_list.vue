<template>
    <div class="content">
        <img src="../assets/logo.png">
        <h1>{{ msg }}</h1>
        <el-row>
        <el-col :span="3" :offset="20"><el-button type="success" @click="btnuseradd">新增</el-button></el-col>
        </el-row>
        <el-table :data="tableData" stripe boder style="width: 100%" v-loading="loading" element-loading-text="拼命加載中">
            <el-table-column type="expand">
                <template scope="props">
                    <el-form label-position="left" inline class="demo-table-expand">
                        <el-form-item label="用戶名">
                            <span>{{ props.row.account }}</span>
                        </el-form-item>
                        <el-form-item label="工號">
                            <span>{{ props.row.card_id }}</span>
                        </el-form-item>
                        <el-form-item label="真實姓名">
                            <span>{{ props.row.real_name }}</span>
                        </el-form-item>
                        <el-form-item label="主管">
                            <span>{{ props.row.boss_id }}</span>
                        </el-form-item>
                        <el-form-item label="郵箱">
                            <span>{{ props.row.email }}</span>
                        </el-form-item>
                        <el-form-item label="創建時間">
                            <span>{{ props.row.creat_time|timeToDate }}</span>
                        </el-form-item>
                        <el-form-item label="編輯人">
                            <span>{{ props.row.handler}}</span>
                        </el-form-item>
                        <el-form-item label="更新時間">
                            <span>{{ props.row.update_time|timeToDate }}</span>
                        </el-form-item>
                    </el-form>
                </template>
            </el-table-column>
            <el-table-column
                    md=8
                    prop="account"
                    label="用戶名"
                    width="180"
                    header-align="center">
            </el-table-column>
            <el-table-column
                    md=8
                    prop="card_id"
                    label="工號"
                    width="180"
                    header-align="center">
            </el-table-column>
            <el-table-column
                    md=8
                    prop="status"
                    label="狀態"
                    header-align="center">
                <template scope="scope">
                    <el-tag :type="scope.row.ustatus?'success':'danger'" close-transition>
                        {{scope.row.ustatus|statusint2str}}
                    </el-tag>
                </template>
            </el-table-column>
            <el-table-column label="操作"
                             header-align="center">
                <template scope="scope">
                    <el-button size="small" @click="handleEdit(scope.$index,scope.row)">編輯</el-button>
                    <el-button size="small" @click="handleDelete(scope.$index,scope.row)" :type="getbtnStatusClass(scope.row.ustatus)">{{scope.row.ustatus|getStatusBtn}}</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!--分頁-->
        <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-sizes="[2,5,10,20,50,100,200]"
                :page-size="pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="totalSize">
        </el-pagination>
        <!--彈窗新增頁面-->
        <el-dialog title="添加用戶" v-model="dialogAddFormVisible">
            <el-form :model="formadd" :rules="rules" label-width="80px">
                <el-form-item label="用戶名" prop="account">
                    <el-input v-model="formadd.account" ></el-input>
                </el-form-item>
                <el-form-item label="工號" prop="card_id">
                    <el-input v-model="formadd.card_id" ></el-input>
                </el-form-item>
                <el-form-item label="真實姓名" prop="real_name">
                    <el-input v-model="formadd.real_name"></el-input>
                </el-form-item>
                <el-form-item label="主管" prop="boss_id">
                    <el-input v-model="formadd.boss_id"></el-input>
                </el-form-item>
                <el-form-item label="郵箱" prop="email">
                    <el-input v-model="formadd.email"></el-input>
                </el-form-item>
                <el-form-item label="密碼" prop="password">
                    <el-input type="password" v-model="formadd.password"></el-input>
                </el-form-item>
                <el-form-item label="確認密碼" prop="password1">
                    <el-input type="password" v-model="formadd.password1"></el-input>
                </el-form-item>
                <el-form-item label="用戶組" prop="os">
                    <el-select v-model="formadd.group_id" placeholder="请选择用戶組">
                        <el-option v-for="item in groupOptions" :label="item.title" :value="item.id"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <div class="dialog-footer" slot="footer">
                <el-button @click="dialogAddFormVisible=false">取消</el-button>
                <el-button type="primary" @click="userAdd">確定</el-button>
            </div>
        </el-dialog>
        <!--彈窗修改頁面-->
        <el-dialog title="修改服務器信息" v-model="dialogFormVisible">
            <el-form :model="form" :rules="rules2" ref="form" label-width="80px">
                <el-form-item label="用戶名" >
                    <span>{{form.account}}</span>
                </el-form-item>
                <el-form-item label="工號" prop="card_id">
                    <el-input v-model="form.card_id" ></el-input>
                </el-form-item>
                <el-form-item label="真實姓名">
                    <el-input v-model="form.real_name"></el-input>
                </el-form-item>
                <el-form-item label="主管" prop="boss_id">
                    <el-input v-model="form.boss_id"></el-input>
                </el-form-item>
                <el-form-item label="郵箱" prop="email">
                    <el-input v-model="form.email"></el-input>
                </el-form-item>
                <el-form-item label="密碼" prop="password" >
                    <el-input type="password" v-model="form.password" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="確認密碼" prop="password1" >
                    <el-input type="password" v-model="form.password1" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="用戶組" prop="os">
                    <el-select v-model="form.group_id" placeholder="请选择用戶組">
                        <el-option v-for="item in groupOptions" :label="item.title" :value="item.id"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <div class="dialog-footer" slot="footer">
                <el-button @click="dialogFormVisible=false">取消</el-button>
                <el-button type="primary" @click="updateServer">確定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>

    import echarts_line from './echarts_line.vue'
    export default {
        data () {
            //新增密碼校驗
            var validatePass=(rule,value,callback)=>{
                if(value===''){
                    callback(new Error('請輸入密碼'))
                }
                else{
                    if(this.formadd.password1!==''){
                        this.$refs.formadd.validateField('checkPass');
                    }
                    callback()
                }
            };
            var validatePass2=(rule,value,callback)=>{
                if(value===''){
                    callback(new Error('請再次輸入密碼'))
                }else if(value!==this.formadd.password){
                    callback(new Error('兩次輸入密碼不一致！'))
                }else{
                    callback()
                }
            };
            //修改密碼校驗
            var validatePass3=(rule,value,callback)=>{
                if(value===''){
                    callback(new Error('請輸入密碼'))
                }
                else{
                    if(this.form.password1!==''){
                        this.$refs.form.validateField('password1');
                    }
                    callback()
                }
            };
            var validatePass4=(rule,value,callback)=>{
                if(value===''){
                    callback(new Error('請再次輸入密碼'))
                }else if(value!==this.form.password){
                    callback(new Error('兩次輸入密碼不一致！'))
                }else{
                    callback()
                }
            };
            return {
                loading:true,
                url:'http://10.130.2.95/v1/',
                msg: '用戶列表!',
                tableData: [],
                multipleSelection:[],
                dialogFormVisible:false,
                dialogAddFormVisible:false,
                groupOptions:[],
                formadd: {
                    account:'',
                    card_id:'',
                    real_name:'',
                    boss_id:'',
                    email:'',
                    password:'',
                    password1:'',
                    group_id:'',
                },
                form: {
                    account:'',
                    card_id:'',
                    real_name:'',
                    boss_id:'',
                    email:'',
                    password:'',
                    password1:'',
                    handler:'',
                    update_time:''
                },
                rules: {//新增驗證規則
                    account:[
                        { required: true, message: '請輸入賬號', trigger: 'blur' },
                    ],
                    card_id: [
                        { required: true, message: '請輸入工號', trigger: 'blur' },
                    ],
                    real_name: [
                        { required: true, message: '姓名不能為空', trigger: 'blur' }
                    ],
                    password: [
                        { validator: validatePass, trigger: 'blur' },
                    ],
                    password1: [
                        { validator: validatePass2, trigger: 'blur' },
                    ],
                },
                rules2: {//修改驗證規則
                    card_id: [
                        { required: true, message: '請輸入工號', trigger: 'blur' },
                    ],
                    real_name: [
                        { required: true, message: '姓名不能為空', trigger: 'change' }
                    ],
                    password: [
                        { validator: validatePass3, trigger: 'blur' },
                    ],
                    password1: [
                        { validator: validatePass4, trigger: 'blur' },
                    ],
                },
                currentPage:1,
                pageSize:5,
                totalSize:0,
            }
        },

        mounted(){
            var self=this;
            var url=this.url+'getUserList';
            this.$http.get(url).then(function(response){
                self.tableData=response.body.userlist
                self.totalSize=response.body.total
                self.groupOptions=response.body.grouplist
                self.loading=false
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
            handleEdit(index,row){//修改資料按鈕
                console.log(index,row);
                this.form=row
                this.form['password1']=this.form['password']
                this.dialogFormVisible=true
            },
            updateServer(){ //修改資料
                var self=this
                var url=this.url+'userUpdate';
                this.$http.post(url,{form:this.form},{
                    'headers':{

                    },emulateJSON:true}).then(function(response){
                        console.log(response.body.editinfo)
                        if(response.body.status){
                            self.$message({message:'修改成功',type:'success'})
                            self.form.handler=response.body.editinfo.editor
                            self.form.update_time=response.body.editinfo.edittime
                            self.dialogFormVisible=false
                        }
                        else
                            self.$message.error(response.body.msg)
                    },function(response){
                        this.msg="erro"
                        sele.$message.error('請求響應錯誤')
                    }
                )
            },
            btnuseradd(){//新增用戶按鈕
                this.dialogAddFormVisible=true
            },
            userAdd(){
                var self=this
                var url=this.url+'userAdd';
                this.$http.post(url,{user:self.formadd},{
                    'headers':{

                    },emulateJSON:true}).then(function(response){
                        console.log(response.body.editinfo)
                        if(response.body.status){
                            self.$message({message:'修改成功',type:'success'})
                            self.tableData.push(response.body.data)
                            self.dialogAddFormVisible=false
                        }
                        else
                            self.$message.error(response.body.msg)
                    },function(response){
                        this.msg="erro"
                        sele.$message.error('請求響應錯誤')
                    }
                )
            },
            handleDelete(index,row){
                console.log(index,row);
                var self=this
                self.form=row
                var url=this.url+'userStatusUpdate';
                this.$http.post(url,{user:row},{
                    'headers':{

                    },emulateJSON:true}).then(function(response){
                        console.log(response.body.editinfo)
                        if(response.body.status){
                            self.$message({message:'修改成功',type:'success'})
                            self.form.ustatus=!self.form.ustatus
                            self.form.handler=response.body.editinfo.editor
                            self.form.update_time=response.body.editinfo.edittime
                            self.dialogFormVisible=false
                        }
                        else
                            self.$message.error(response.body.msg)
                    },function(response){
                        this.msg="erro"
                        sele.$message.error('請求響應錯誤')
                    }
                )
            },
            //分頁處理函數分頁大小調整
            handleSizeChange(val) {
                this.pageSize=val
                this.currentPage = 1;
                console.log(`每页 ${val} 条`);
                var self=this
                var url=this.url+'getUserList';
                console.log(this.form.os);
                getserverList(self,url);
                /*this.$http.post(url,{page:1,limit:this.pageSize},{
                    'headers':{

                    },emulateJSON:true}).then(function(response){
                        if(response.body.status){
                            self.tableData=response.body.subjects
                        }
                        else
                            self.$message.error(response.body.msg)
                    },function(response){
                        self.$message.error('請求響應錯誤')
                    }
                )*/
            },
            //分頁處理函數分頁選擇
            handleCurrentChange(val) {
                this.currentPage = val;
                console.log(`当前页: ${val}`);
                var self=this
                var url=this.url+'getUserList';
                console.log(this.form.os);
                getserverList(self,url);
                /*this.$http.post(url,{page:val,limit:this.pageSize},{
                    'headers':{

                    },emulateJSON:true}).then(function(response){
                        if(response.body.status){
                            self.tableData=response.body.subjects
                        }
                        else
                            self.$message.error(response.body.msg)
                    },function(response){
                        self.$message.error('請求響應錯誤')
                    }
                )*/
            },
            //批量啟用
            start(){
                console.log(this.multipleSelection)
                if(this.multipleSelection.length>0){
                    var self=this
                    var url=this.url+'updateServerStatus';
                    this.$http.post(url,{form:this.multipleSelection,status:1},{
                        'headers':{

                        },emulateJSON:true}).then(function(response){
                            console.log(response.body.name)
                            if(response.body.status){
                                self.$message({message:'修改成功',type:'success'})
                                for(var item in self.multipleSelection){
                                    self.multipleSelection[item].isValid=1
                                }
                            }
                            else
                                self.$message.error(response.body.msg)
                        },function(response){
                            this.msg="erro"
                            sele.$message.error('請求響應錯誤')
                        }
                    )
                }
                else
                    this.$message.error("未選擇服務器")
            },
            stop(){
                if(this.multipleSelection.length>0){
                    var self=this
                    var url=this.url+'updateServerStatus';
                    this.$http.post(url,{form:this.multipleSelection,status:0},{
                        'headers':{

                        },emulateJSON:true}).then(function(response){
                            console.log(response.body.name)
                            if(response.body.status){
                                self.$message({message:'修改成功',type:'success'})
                                for(var item in self.multipleSelection){
                                    self.multipleSelection[item].isValid=0
                                }
                            }
                            else
                                self.$message.error(response.body.msg)
                        },function(response){
                            this.msg="erro"
                            sele.$message.error('請求響應錯誤')
                        }
                    )
                }
                else
                    this.$message.error("未選擇服務器")
            },
            getbtnStatusClass:function (status) {
                return status?"danger":'success'
            }

        },
        filters:{//計算屬性
            remarksShow:function(remarks){
                var len=remarks.remark1.length+remarks.remark2.length+remarks.remark3.length+remarks.remark4.length
                if(len)
                    return remarks.remark1+';'+remarks.remark2+';'+remarks.remark3+';'+remarks.remark4
                else
                    return ''
                },
            timeToDate:function(val){ //時間戳轉時間
                if(!isNaN(val)){
                var t=new Date(val*1000)
                var year=t.getFullYear(),mon=t.getMonth()+1,day=t.getDate()
                var hour=t.getHours(),min=t.getMinutes(),sec=t.getSeconds()
                return year+'-'+mon+'-'+day+' '+hour+':'+min+':'+sec
                }
                return val;
            },
            warrantyToDate:function(val){//維保期轉日期
                if(val){
                    var t=new Date(val*1000)
                    var year=t.getFullYear(),mon=t.getMonth()+1,day=t.getDate()
                    var hour=t.getHours(),min=t.getMinutes(),sec=t.getSeconds()
                    return year+'-'+mon+'-'+day
                }
            },
            statusint2str:function(value){//狀態轉換
                return value?'有效':'無效'
            },
            getStatusBtn:function(type){
                return type?'禁用':'啟用'
            }

        },
        components:{'linechart':echarts_line}
    }
    function getserverList(self,url) {
        self.loading=true
        self.$http.post(url,{page:self.currentPage,limit:self.pageSize},{
            'headers':{

            },emulateJSON:true}).then(function(response){
                if(response.body.status){
                    self.tableData=response.body.userlist
                }
                else
                    self.$message.error(response.body.msg)
                self.loading=false
            },function(response){
                self.$message.error('請求響應錯誤')
            }
        )
    }
</script>
<style>
    .demo-table-expand {
        font-size: 0;
    }
    .demo-table-expand label {
        width: 90px;
        color: #99a9bf;
    }
    .demo-table-expand .el-form-item {
        margin-right: 0;
        margin-bottom: 0;
        width: 50%;
    }
</style>