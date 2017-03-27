<template>
    <div class="chartLineContent">
        <div :id="line_id" style="width:500px;height:400px">{{line_id}}</div>
    </div>
</template>

<script>

    //getMaxmin
    //maxmin=max or min
    function getMaxmin(arr,maxmin){
        if(maxmin=='max'){
            return Math.max.apply(Math,arr);
        }
        else if(maxmin=='min'){
            return Math.min.apply(Math,arr);
        }
    }

    export default {
        data () {
            return {
                msg: 'Use Vue 2.0 Today!',
                intervalid:''
            }
        },
        props:['line_id','line_title','datach','datech','axislab'],

        mounted(){
            var self=this;
            //tubiao
            if(self.line_title=="内存"){

            }
            var option = {
                title:{
                    text: this.line_title,
                    left: 'center',
                    top: 20,
                    textStyle: {
                        color: '#000'
                    }
                },
                tooltip:{
                    trigger:'axis'
                },
                xAxis: {
                    type: 'category',
                    boundaryGap: false,
                    data: self.datech
                },
                yAxis: {
                    axisLabel:{
                        formatter:function (value) {
                            return value+self.axislab;
                        }
                    },
                    boundaryGap: [0, '50%'],
                    type: 'value',
                    splitNumber:5
                },
                series: [
                    {
                        name:this.line_title+'信息',
                        type:'line',
                        smooth:true,
                        symbol: 'circle',
                        stack: 'a',
                        areaStyle: {
                            normal: {}
                        },
                        data: self.datach
                    }
                ]
            };
            //chushihua
            /*var arr=this.datach;
            for (var i = 0; i < 5; i++) {
                console.log(i);
                console.log(arr);
                console.log(arr[i]);
                /!*var tem=self.datach[i]["date"];
                tem =tem.substr(8,tem.lenght);
                date1.push(tem)
                data.push(parseFloat(this.datach[i].Process['Working Set']._total)/1024/1024)*!/
            }*/
            //chushihua
            var echarts=require('echarts');
            var myChart=echarts.init(document.getElementById(this.line_id));
            var loadflag=1;
            myChart.showLoading()
            myChart.setOption(option);
            self.intervalid=setInterval(function () {
                if(loadflag&&self.datech.length>0){
                    loadflag=0;
                    myChart.hideLoading()
                }
             myChart.setOption({
             xAxis: {
             data: self.datech
             },
             yAxis:{
             max:parseInt(getMaxmin(self.datach,'max')) +10,
             min:parseInt(getMaxmin(self.datach,'min'))-10
             },
             series: [{
             name:self.line_title+'信息',
             data: self.datach
             }]
             });
             }, 3000);
            /*var setintervalid=setInterval(function () {
                /!*self.$http.get(url,{},{
                    headers:{
                        "X-Request-With":"XMLHttpRequest",
                    }
                }).then(response=>{
                    var tem=response.body[0]["date"];
                    tem=tem.substr(8,tem.length)
                    date1.push(tem)
                    data.push(parseFloat(response.body[0].Process['Working Set']._total/1024/1024))
                    date1.shift()
                    data.shift()
                })*!/
                console.log(self.datach);
                if(self.datach!=undefined){
                    for (var i = self.count-1; i >=0; i--) {
                        console.log(i);
                        console.log(arr);
                        console.log(arr[i]);
                        var tem=self.datach[i]["date"];
                         tem =tem.substr(8,tem.lenght);
                         date1.push(tem)
                         data.push(parseFloat(self.datach[i].Process['Working Set']._total)/1024/1024)
                    }
                myChart.setOption({
                    xAxis: {
                        data: date1
                    },
                    yAxis:{
                        max:getMaxmin(data,'max')+10,
                        min:getMaxmin(data,'min')-10
                    },
                    series: [{
                        name:'成交',
                        data: data
                    }]
                });
                    clearInterval(setintervalid)
                }
            }, 5000);*/

        },
        methods: {
            startHacking () {
                console.log(this.datach);
                console.log(this.datech);
                this.$notify({
                    title: 'It Works',
                    message: 'We have laid the groundwork for you. Now it\'s your time to build something epic!',
                    duration: 6000
                })
            },
            gettoken(){
                this.$http.get('http://10.130.2.95/indexapi').then(response=>{
                    alert(response.data.msg);
                })
            }

        },
        beforeDestroy(){
            console.log('intervalid1:'+this.intervalid)
            clearInterval(this.intervalid)
        },

    }
</script>