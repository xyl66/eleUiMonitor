<template>
    <div class="content">
        <img src="../assets/logo.png">
        <h1>{{ msg }}</h1>
        <el-row>
            <el-col :span="4" :offset="20">
                <el-button type="success" @click="start">啟用</el-button>
                <el-button type="success" @click="stop">停用</el-button>
            </el-col>
        </el-row>
        <el-table :data="tableData" stripe boder style="width: 100%" @selection-change="handleSelectionChange" v-loading="loading" element-loading-text="拼命加載中">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column type="expand">
                <template scope="props">
                    <el-form label-position="left" inline class="demo-table-expand">
                        <el-form-item label="Ip">
                            <span>{{ props.row.ip }}</span>
                        </el-form-item>
                        <el-form-item label="系統版本">
                            <span>{{ props.row.os }}</span>
                        </el-form-item>
                        <el-form-item label="硬盤">
                            <span>{{ props.row.disk }}</span>
                        </el-form-item>
                        <el-form-item label="規格">
                            <span>{{ props.row.size }}</span>
                        </el-form-item>
                        <el-form-item label="內存">
                            <span>{{ props.row.ram }}</span>
                        </el-form-item>
                        <el-form-item label="CPU數量">
                            <span>{{ props.row.cpuNum }}</span>
                        </el-form-item>
                        <el-form-item label="備註">
                            <span>{{ props.row.remarks|remarksShow }}</span>
                        </el-form-item>
                        <el-form-item label="維保期">
                            <span>{{ props.row.warranty|warrantyToDate }}</span>
                        </el-form-item>
                        <el-form-item label="創建人">
                            <span>{{ props.row.creator }}</span>
                        </el-form-item>
                        <el-form-item label="創建日期">
                            <span>{{ props.row.createDate|timeToDate }}</span>
                        </el-form-item>
                        <el-form-item label="修改人">
                            <span>{{ props.row.modified}}</span>
                        </el-form-item>
                        <el-form-item label="修改日期">
                            <span>{{ props.row.modifyDate|timeToDate}}</span>
                        </el-form-item>
                    </el-form>
                </template>
            </el-table-column>
            <el-table-column
                    md=8
                    prop="ip"
                    label="Ip"
                    width="180"
                    header-align="center">
            </el-table-column>
            <el-table-column
                    md=8
                    prop="os"
                    label="系統版本"
                    width="180"
                    header-align="center">
            </el-table-column>
            <el-table-column
                    md=8
                    prop="isValid"
                    label="狀態"
                    header-align="center">
                <template scope="scope">
                    <el-tag :type="scope.row.isValid?'success':'danger'" close-transition>
                        {{scope.row.isValid|statusint2str}}
                    </el-tag>
                </template>
            </el-table-column>
            <el-table-column label="操作"
                             header-align="center">
                <template scope="scope">
                    <el-button size="small" @click="handleEdit(scope.$index,scope.row)">編輯</el-button>
                    <el-button size="small" type="danger" @click="handleDelete(scope.$index,scope.row)">刪除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-sizes="[2,5,10,20,50,100,200]"
                :page-size="pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="totalSize">
        </el-pagination>
        <!--彈窗修改頁面-->
        <el-dialog title="修改服務器申請信息" v-model="dialogFormVisible">
            <el-form ref="form" :model="form" :rules="rules" label-width="100px">
                <el-form-item label="OS版本" prop="os">
                    <el-col :span="5">
                        <!--<el-autocomplete
                                class="inline-input"
                                v-model="form.os"
                                :fetch-suggestions="querySearch"
                                placeholder="请輸入操作系統版本"
                                @select="handleSelect"
                        ></el-autocomplete>-->
                        <el-select v-model="form.os" placeholder="请輸入操作系統版本" @change="handleSelect">
                            <el-option
                                    v-for="item in osoptions"
                                    :label="item.label"
                                    :value="item.value">
                            </el-option>
                        </el-select>
                    </el-col>
                </el-form-item>
                <el-form-item label="硬盤容量" prop="disk">
                    <el-col :span="5">
                        <el-select v-model="form.disk" placeholder="請選擇" @change="handleDiskChange">
                            <el-option
                                    v-for="item in diskoptions"
                                    :label="item.label"
                                    :value="item.value">
                            </el-option>
                        </el-select>
                    </el-col>
                    <el-col :span="12">
                        <el-input v-model="form.spaceD" v-if="dShow" placeholder="請輸入D盤大小"></el-input>
                    </el-col>
                </el-form-item>
                <el-form-item label="內存大小" prop="ram">
                    <el-input v-model="form.ram"></el-input>
                </el-form-item>
                <el-form-item label="CPU數量" prop="cpuNum">
                    <el-input v-model="form.cpuNum"></el-input>
                </el-form-item>
                <el-form-item label="備註">
                    <el-col :span="6">
                        <el-input v-model="form.remarks.remark1" placeholder="所屬部門/負責人">
                            <template slot="append">,</template>
                        </el-input>
                    </el-col>
                    <el-col :span="6">
                        <el-input v-model="form.remarks.remark2" placeholder="用途">
                            <template slot="append">,</template>
                        </el-input>
                    </el-col>
                    <el-col :span="6">
                        <el-input v-model="form.remarks.remark3" placeholder="購買日期">
                            <template slot="append">,</template>
                        </el-input>
                    </el-col>
                    <el-col :span="6"><el-input v-model="form.remarks.remark4" placeholder="上架日期"></el-input></el-col>
                </el-form-item>
                <el-form-item label="計算機名" prop="sn_Description">
                    <el-input v-model="form.sn_Description"></el-input>
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
            return {
                loading:true,
                diskoptions:[],
                osoptions:[{ "value": "Windows Service2008"},
                    { "value": "Windows Service2012"},
                    { "value": "Windows Service2016"},
                    { "value": "Red Hat6.5"},
                    { "value": "Red Hat7.2"},
                    { "value": "CentOS6.5"},
                    { "value": "CentOS7.2"},
                    { "value": "Ubuntu14"},
                    { "value": "Ubuntu16"},],
                dShow:0,
                url:'http://10.130.2.95/v1/',
                msg: '申請列表!',
                tableData: [],
                multipleSelection:[],
                dialogFormVisible:false,
                form: {
                    ip:'',
                    type:'',
                    size:'',
                    os:'',
                    disk:'',
                    ram:'',
                    cpuNum:'',
                    remarks:{
                        remark1:'',
                        remark2:'',
                        remark3:'',
                        remark4:''
                    },
                    location:'',
                    sn_Description:'',
                    date1:'',
                    date2:'',
                    warranty:'',
                    ilo:'',
                    isValid:false,
                    creator:'',
                    createDate:'',
                    modified:'',
                    modifyDate:'',
                },
                rules: {
                    ip: [
                        { required: true, message: '请输入服務器Ip', trigger: 'blur' },
                    ],
                    type: [
                        { required: true, message: '请选择類型', trigger: 'change' }
                    ],
                    os: [
                        { required: true, message: '请选择系統版本', trigger: 'change' }
                    ],
                    disk: [
                        { required: true, message: '請輸入硬盤大小', trigger: 'blur' },
                    ],
                    ram: [
                        { required: true, message: '請輸入內存大小', trigger: 'blur' },
                    ],
                    cpuNum: [
                        { required: true, message: '請輸入CPU數量', trigger: 'blur' },
                    ],
                    sn_Description: [
                        { required: true, message: '請填寫編號/名稱', trigger: 'change' }
                    ],
                },
                currentPage:1,
                pageSize:5,
                totalSize:0,
            }
        },

        mounted(){
            var self=this;
            var url=this.url+'getServerApplyList';
            this.$http.get(url).then(function(response){
                self.tableData=response.body.subjects
                self.totalSize=response.body.total
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
            updateServer(){ //修改資料
                var self=this
                var url=this.url+'updateServerApply';
                this.form.warranty=parseInt((new Date(this.form.date1).getTime())/1000)
                console.log(this.form.os);
                this.$http.post(url,{form:this.form,apply:1},{ //serviceapply表ordeviceinfo表
                    'headers':{

                    },emulateJSON:true}).then(function(response){
                        console.log(response.body.name)
                        if(response.body.status){
                            self.$message({message:'修改成功',type:'success'})
                            self.form.modified=response.body.editinfo.editor
                            self.form.modifyDate=response.body.editinfo.edittime
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
            handleEdit(index,row){
                console.log(index,row);
                this.form=row
                if(row.disk.indexOf('D')>0)
                    this.dShow=1
                else
                    this.dShow=0
                this.dialogFormVisible=true
            },
            handleDelete(index,row){
                console.log(index,row);
            },
            handleSelectionChange(val){
                this.multipleSelection=val
                console.log(this.multipleSelection)
            },
            //分頁處理函數分頁大小調整
            handleSizeChange(val) {
                this.pageSize=val
                this.currentPage = 1;
                console.log(`每页 ${val} 条`);
                var self=this
                var url=this.url+'getServerApplyList';
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
                var url=this.url+'getServerApplyList';
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
            //OS選擇
            handleSelect(item) {
                console.log(item);
                console.log(item.indexOf('Windows'));
                this.form.disk=''
                if(item.indexOf("Windows")>=0){
                    this.diskoptions=[{label:'C盤',value:'C盤'},{label:'C盤+D盤',value:'C盤+D盤'}]
                }
                else
                    this.diskoptions=[{label:'Boot 500MB,VG(SWAP 8G,ROOT 50G-60G)',value:'Boot 500MB,VG(SWAP 8G,ROOT 50G-60G)'},{label:'Boot 500MB,VG(SWAP 8G,ROOT 50G-60G),Home',value:'Boot 500MB,VG(SWAP 8G,ROOT 50G-60G),Home'}]
            },
            //OS清除

            //windows顯示D
            handleDiskChange(item){
                console.log(item);
                if(item.indexOf('D')>0)
                    this.dShow=1
                else
                    this.dShow=0
            },
            //批量啟用
            start(){
                console.log(this.multipleSelection)
                if(this.multipleSelection.length>0){
                    var self=this
                    var url=this.url+'updateServerApplyStatus';
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
                    var url=this.url+'updateServerApplyStatus';
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
                var t=new Date(val*1000)
                var year=t.getFullYear(),mon=t.getMonth()+1,day=t.getDate()
                var hour=t.getHours(),min=t.getMinutes(),sec=t.getSeconds()
                return year+'-'+mon+'-'+day+' '+hour+':'+min+':'+sec
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
                    self.tableData=response.body.subjects
                }
                else
                    self.$message.error(response.body.msg)
                slef.loading=false
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