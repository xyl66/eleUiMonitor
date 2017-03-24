<template>
    <div class="content">
        <el-form ref="form" :model="form" :rules="rules" label-width="100px">
            <el-form-item label="IP地址" prop="ip">
                <el-input v-model="form.ip"></el-input>
            </el-form-item>
            <el-form-item label="類型" prop="type">
                <el-select v-model="form.type" placeholder="请选择服務器類型">
                    <el-option label="實體機" value="1"></el-option>
                    <el-option label="虛擬機" value="0"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="規格">
                <el-input v-model="form.size"></el-input>
            </el-form-item>
            <el-form-item label="OS版本" prop="os">
                <el-autocomplete
                        class="inline-input"
                        v-model="form.os"
                        :fetch-suggestions="querySearch"
                        placeholder="请輸入操作系統版本"
                        @select="handleSelect"
                ></el-autocomplete>
                <!--<el-select v-model="form.os" placeholder="请选择操作系統版本">
                    <el-option label="区域一" value="shanghai"></el-option>
                    <el-option label="区域二" value="beijing"></el-option>
                </el-select>-->
            </el-form-item>
            <el-form-item label="硬盤容量" prop="disk">
                <el-input v-model="form.disk"></el-input>
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
            <el-form-item label="所屬位置">
                <el-input v-model="form.location"></el-input>
            </el-form-item>
            <el-form-item label="編號/名稱" prop="sn_Description">
                <el-input v-model="form.sn_Description"></el-input>
            </el-form-item>
            <el-form-item label="維保期">
                <el-col :span="11">
                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date1" style="width: 100%;"></el-date-picker>
                </el-col>
            </el-form-item>
            <el-form-item label="ILO">
                <el-input v-model="form.ilo"></el-input>
            </el-form-item>
            <el-form-item label="是否有效">
                <el-switch on-text="" off-text="" v-model="form.isValid"></el-switch>
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
                    warranty:'',
                    ilo:'',
                    isValid:false,
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
                }
            }
        },
        methods: {
            onSubmit() {
                var self=this
                var url='http://10.130.2.95/v1/test';
                this.form.warranty=parseInt((new Date(this.form.date1).getTime())/1000)
                console.log(this.form.os);
                this.$http.post(url,{form:this.form},{
                    'headers':{

                    },emulateJSON:true}).then(function(response) {
                    if (response.body.status) {
                        self.$message({message: '添加成功', type: 'success'})
                        this.$router.push({path:'/serverList'});
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
                    { "value": "Linux"},
                    { "value": "Win7"},
                    { "value": "WinXp"},
                    { "value": "Winservice2008"},
                    { "value": "Winservice2012"},
                    { "value": "Mac Os"},

                ];
            },
            handleSelect(item) {
                console.log(item);
            }
        },
        mounted() {
            this.restaurants = this.loadAll();//系統版本選項預處理
        }
    }
</script>