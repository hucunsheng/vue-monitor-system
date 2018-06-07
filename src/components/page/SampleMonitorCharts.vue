<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 图表</el-breadcrumb-item>
                <el-breadcrumb-item>柱状图</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <el-form ref="form" :model="form" label-width="80px">
	            <el-form-item label="开始时间">
	                <el-col :span="5">
	                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date1" value-format="yyyy-MM-dd" style="width: 100%;"></el-date-picker>
	                </el-col>
	                <el-col class="line" :span="1">-</el-col>
	                <el-col :span="5">
	                    <el-time-picker placeholder="选择时间" v-model="form.time1" value-format="HH:mm" style="width: 100%;"></el-time-picker>
	                </el-col>
	            </el-form-item>
	            <el-form-item label="结束时间">
	                <el-col :span="5">
	                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date2" value-format="yyyy-MM-dd" style="width: 100%;"></el-date-picker>
	                </el-col>
	                <el-col class="line" :span="1">-</el-col>
	                <el-col :span="5">
	                    <el-time-picker placeholder="选择时间" v-model="form.time2" value-format="HH:mm" style="width: 100%;"></el-time-picker>
	                </el-col>
	            </el-form-item>
	            <el-col :span="5">
	            	<el-select  v-model="select_data" placeholder="请选择" class="handle-select mr10" style="display: block;width: 200px;">
		                    <el-option  v-for="item in options" :key="item.MONITOR_CON_PARA_ID" :label="item.PARA_SERVICENAME" :value="item.MONITOR_CON_PARA_ID" ></el-option>
		            </el-select>
	            </el-col>
	            <el-col class="line" :span="1">-</el-col>
	            <el-col :span="5">
	                
	                <el-form-item>
                        <el-button type="primary" @click="initChart" style="margin-left: 20px;">统计</el-button>
                    </el-form-item>
	            </el-col>
            </el-form>
	            <el-col class="line"></el-col>
            <div  >

            		<div ref="myEchart" style="width:800px;height:400px"></div>
            </div>

        </div>
    </div>
</template>

<script>
    import echarts from 'echarts';
    export default {
        components: {
            echarts
        },
        data()  {
        		return {
	            options: [
	                { text: 'One', value: 'A' },
	                { text: 'Two', value: 'B' },
	                { text: 'Three', value: 'C' }
	            ],
	            select_data: '',
	            form: {
                    name: '',
                    region: '',
                    date1: this.getDateTime(1,-1).split(' ')[0],
                    time1: this.getDateTime(1,-1).split(' ')[1],
                    date2: this.getDateTime(1,0).split(' ')[0],
                    time2: this.getDateTime(1,0).split(' ')[1],
                    delivery: true,
                    type: ['步步高'],
                    resource: '小天才',
                    desc: '',
                    options: []
                }
           }
        },
        mounted () {
        	
	        	var url  = "/monitor/getMonitors";                
            // 发送请求:将数据返回到一个回到函数中
            // 并且响应成功以后会执行then方法中的回调函数
            this.$axios.get(url, {
               }).then((response) => {
//                  console.log(result.data.message[0]);
//					alert("response==="+response);
//		            alert("response.data==="+response.data);
		            var selectList = response.data.selectList;
//		            alert("selectList==="+selectList);
//		            alert("this.$data.options==="+this.$data.options);
					this.$data.options = selectList;
//					alert("this.$data.options==="+this.$data.options);
		            this.$data.select_data = selectList[0].MONITOR_CON_PARA_ID;
		            
//		            this.$data.couponSelected=selectList[0].MONITOR_CON_PARA_ID;
		            //init();
		          
		            //alert("date1=="+this.getDateTime(1,-1).split(' ')[0]);
		            //alert("time1=="+this.getDateTime(1,-1).split(' ')[1]);
		            //alert("date2=="+this.getDateTime(1,0).split(' ')[0]);
		            //alert("time2=="+this.getDateTime(1,0).split(' ')[1]);
		            this.$data.form.time1 = this.getDateTime(1,-1).split(' ')[1];
		            //alert("this.$data.form.time1=="+this.$data.form.time1);
		            this.initChart();
//		            alert(this.form.time2);
                })
        },
		methods: {
			initChart() {
				this.chart = echarts.init(this.$refs.myEchart);
				var url = "/monitor/getEcharts";
				var fromval = this.$data.form.date1+" "+this.$data.form.time1;
		        var toval = this.$data.form.date2+" "+this.$data.form.time2;
		        var params = new URLSearchParams();
				params.append('from', fromval);
				params.append('to', toval);
				params.append('monitorId', this.$data.select_data);
				var nText = this.$data.options.find(item => item.MONITOR_CON_PARA_ID === this.$data.select_data)['PARA_SERVICENAME']
		        this.$axios.post(url, params).then((response) => {
		                var resName = response.data.resName;
		                var xAxisList = response.data.xAxisList;
		                var seriesList = response.data.seriesList;
		                var option = {
		                    title : {
//								text :nText
		                    },
		                    tooltip: {
		                        show: true
		                    },
		                    legend: {
		                        data: [resName]
		                    },
		                    xAxis: [
		                        {
		                            type: 'category',
		                            data: xAxisList
		                        }
		                    ],
		                    yAxis: [
		                        {
		                            type: 'value'
		                        }
		                    ],
		                    series: [
		                        {
		                            "name": resName,
		                            "type": "bar",
		                            "data": seriesList,
		                            markPoint : {
		                                data : [
		                                    {type : 'max', name: '最大值'},
		                                    {type : 'min', name: '最小值'}
		                                ]
		                            },
		                            markLine : {
		                                data : [
		                                    {type : 'average', name : '平均值'}
		                                ]
		                            }
		                        }
		                    ]
		                };
		
		
		                // 为echarts对象加载数据
		                this.chart.setOption(option);
		            })

			},
			getDateTime(data,hour){
				  let myDate;
				  if(data !== 1){
				    myDate = new Date(data * 1000);
				  }else{
				    myDate = new Date();
				  }
				
				  let Y = myDate.getFullYear(),
				      M = myDate.getMonth() + 1,
				      D = myDate.getDate(),
				      H = myDate.getHours() + hour,
				      Min = myDate.getMinutes(),
				      S = myDate.getSeconds();
				
				  if(M < 10){
				    M = '0' + M ;
				  }
				  if(D < 10){
				    D = '0' + D ;
				  }
				  if(H < 10){
				    H = '0' + H ;
				  }
				  if(Min < 10){
				    Min = '0' + Min ;
				  }
				  if(S < 10){
				    S = '0' + S ;
				  }
				  return Y + '-' + M + '-' + D + ' ' + H + ':' + Min + ':' + S;
			}
		}
   } 
</script>

<style scoped>
    .schart{
        width: 600px;
        display: inline-block;
        margin-top: 20px;
    }
    .content-title{
        clear: both;
        font-weight: 400;
        line-height: 50px;
        margin: 10px 0;
        font-size: 22px;
        color: #1f2f3d;
    }
    
</style>