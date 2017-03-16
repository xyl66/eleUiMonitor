<template>
    <div class="chartLineContent">
        <div :id="pie_id" style="width:100px;height:100px">{{pie_id}}</div>
    </div>
</template>

<script>
    export default{
        data(){
            return{}
        },
        props:['pie_id','datach','pie_title'],
        mounted(){
            //chushihuapie
            var option = {
                title: {
                    text: this.pie_title,
                    left: 'center',
                    top: 0,
                    textStyle: {
                        color: '#000',
                        fontSize:1
                    }
                },

                tooltip : {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                color:['#2DC216','#C23B28'],
                series : [
                    {
                        name:'內存信息',
                        type:'pie',
                        radius : '55%',
                        center: ['50%', '50%'],
                        data:[
                        ],

                        minAngle:30,
                        label: {
                            normal: {
                                show:false,
                                textStyle: {
                                    color: 'rgba(0, 0, 0, 0.7)',
                                }
                            }
                        },
                        labelLine: {
                            normal: {
                                show:false,
                                lineStyle: {
                                    color: 'rgba(0, 0, 0, 0.7)'
                                },
                                smooth: 0.2,
                                length: 10,
                                length2: -10
                            }
                        },
                        animationType: 'scale',
                        animationEasing: 'elasticOut',
                        animationDelay: function (idx) {
                            return Math.random() * 200;
                        }
                    }
                ]
            };
            var self=this
            var echarts=require('echarts');
            var myChart=echarts.init(document.getElementById(this.pie_id));
            var loadflag=1;
            myChart.showLoading()
            myChart.setOption(option);
            self.intervalid=setInterval(function () {
                if(loadflag&&self.datach.length>0){
                    loadflag=0
                    myChart.hideLoading()
                }
                myChart.setOption({
                    series: [{
                        data:[
                            {value:self.datach[0], name:'剩余内存'},
                            {value:100-self.datach[0], name:'已使用'},
                        ],
                    }]
                });
            }, 3000);
        },
        beforeDestroy(){
            console.log('intervalid0:'+this.intervalid)
            clearInterval(this.intervalid)
        }
    }
</script>