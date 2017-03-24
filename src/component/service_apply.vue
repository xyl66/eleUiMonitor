<template>
    <div class="content">
        <el-form ref="form" :model="form" :rules="rules" label-width="100px">
            <el-form-item label="OS版本" prop="os">
                <el-col :span="5">
                <el-autocomplete
                        class="inline-input"
                        v-model="form.os"
                        :fetch-suggestions="querySearch"
                        placeholder="请輸入操作系統版本"
                        @select="handleSelect"
                ></el-autocomplete>
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
            <el-form-item>
                <el-button type="primary" @click="onSubmit">立即创建</el-button>
                <el-button>取消</el-button>
            </el-form-item>
        </el-form>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                diskoptions:[],
                dShow:0,
                form: {
                    ip:'',
                    type:'',
                    size:'',
                    os:'',
                    disk:'',
                    spaceD:'',
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
                    warranty:'',
                    ilo:'',
                    isValid:false,
                },
                rules: {
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
                        { required: true, message: '請填寫計算機名', trigger: 'change' }
                    ],
                }
            }
        },
        methods: {
            onSubmit() {
                var self=this
                var url='http://10.130.2.95/v1/serviceApply';
                console.log(this.form.os);
                this.$http.post(url,{form:this.form},{
                    'headers':{

                    },emulateJSON:true}).then(function(response) {
                        if (response.body.status) {
                            self.$message({message: '添加成功', type: 'success'})
                            /*this.$router.push({path:'/serverList'});*/
                        }
                        else {
                            self.$message.error(response.body.msg)
                        }
                    },function(response){
                        this.msg="erro"
                        sele.$message.error('請求響應錯誤')
                    }
                )
            },
            //系統版本選項預處理
            querySearch(queryString, cb) {
                var restaurants = this.restaurants;
                var results = queryString ? restaurants.filter(this.createFilter(queryString)) : restaurants;
                // 调用 callback 返回建议列表的数据
                cb(results);
            },
            createFilter(queryString) {
                return (restaurant) => {
                    return (restaurant.value.indexOf(queryString.toLowerCase()) === 0);
                };
            },
            loadAll() {
                return [
                    { "value": "Windows Service2008"},
                    { "value": "Windows Service2012"},
                    { "value": "Windows Service2016"},
                    { "value": "Red Hat6.5"},
                    { "value": "Red Hat7.2"},
                    { "value": "CentOS6.5"},
                    { "value": "CentOS7.2"},
                    { "value": "Ubuntu14"},
                    { "value": "Ubuntu16"},

                ];
            },
            //OS選擇
            handleSelect(item) {
                console.log(item.value);
                console.log(item.value.indexOf('Windows'));
                this.form.disk=''
                if(item.value.indexOf("Windows")>=0){
                    this.diskoptions=[{label:'C盤',value:'C盤'},{label:'C盤+D盤',value:'C盤+D盤'}]
                }
                else
                    this.diskoptions=[{label:'Boot 500MB,VG(SWAP 8G,ROOT 50G-60G)',value:'Boot 500MB,VG(SWAP 8G,ROOT 50G-60G)'},{label:'Boot 500MB,VG(SWAP 8G,ROOT 50G-60G),Home',value:'Boot 500MB,VG(SWAP 8G,ROOT 50G-60G),Home'}]
            },
            //windows顯示D
            handleDiskChange(item){
                console.log(item);
                if(item.indexOf('D')>0)
                    this.dShow=1
            }
        },
        mounted() {
            this.restaurants = this.loadAll();//系統版本選項預處理
        }
    }
</script>