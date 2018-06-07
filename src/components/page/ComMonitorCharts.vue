<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 图表</el-breadcrumb-item>
                <el-breadcrumb-item>柱状图</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <el-form ref="form" :model="form" label-width="100px">
	            <el-form-item label="监控指标T">
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
			            	<el-form-item label="监控类型">
				            	<el-select  v-model="select_data1" placeholder="请选择" class="handle-select mr10" style="display: block;width: 200px;">
				                    <el-option  v-for="item in options" :key="item.MONITOR_CON_PARA_ID" :label="item.PARA_SERVICENAME" :value="item.MONITOR_CON_PARA_ID" ></el-option>
				            </el-select>
			            </el-form-item>
		            </el-col>
				</el-form-item>
	            	<el-col class="line" ></el-col>
	            <el-form-item label="监控指标T-1">
		            <el-form-item label="开始时间">
		                <el-col :span="5">
		                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date3" value-format="yyyy-MM-dd" style="width: 100%;"></el-date-picker>
		                </el-col>
		                <el-col class="line" :span="1">-</el-col>
		                <el-col :span="5">
		                    <el-time-picker placeholder="选择时间" v-model="form.time3" value-format="HH:mm" style="width: 100%;"></el-time-picker>
		                </el-col>
		            </el-form-item>
		            <el-form-item label="结束时间">
		                <el-col :span="5">
		                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date4" value-format="yyyy-MM-dd" style="width: 100%;"></el-date-picker>
		                </el-col>
		                <el-col class="line" :span="1">-</el-col>
		                <el-col :span="5">
		                    <el-time-picker placeholder="选择时间" v-model="form.time4" value-format="HH:mm" style="width: 100%;"></el-time-picker>
		                </el-col>
		            </el-form-item>
	            		<el-col :span="11">
		            	<el-form-item label="监控类型">
			            	<el-select  v-model="select_data2" placeholder="请选择" class="handle-select mr10" style="display: block;width: 200px;">
			                    <el-option  v-for="item in options2" :key="item.MONITOR_CON_PARA_ID" :label="item.PARA_SERVICENAME" :value="item.MONITOR_CON_PARA_ID" ></el-option>
			            </el-select>
		            </el-form-item>
		            </el-col>
		            <el-col :span="5">
			            	<el-form-item>
		                    <el-button type="primary" @click="initChart" style="margin-left: 20px;">统计</el-button>
		                </el-form-item>
		            </el-col>
	            </el-form-item>
            </el-form>
            <el-col class="line"></el-col>
            <div  >
               <!-- <schart canvasId="bar" width="500" height="400" :data="data1" type="bar" :options="options1"></schart>
            --> 
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
	            options2: [
	                { text: 'One', value: 'A' },
	                { text: 'Two', value: 'B' },
	                { text: 'Three', value: 'C' }
	            ],
	            select_data1: '',
	            select_data2: '',
	            form: {
                    name: '',
                    region: '',
                    date1: this.getDateTime(1,0,-1).split(' ')[0],
                    time1: this.getDateTime(1,0,-1).split(' ')[1],
                    date2: this.getDateTime(1,0,0).split(' ')[0],
                    time2: this.getDateTime(1,0,0).split(' ')[1],
                    date3: this.getDateTime(1,-1,-1).split(' ')[0],
                    time3: this.getDateTime(1,-1,-1).split(' ')[1],
                    date4: this.getDateTime(1,-1,0).split(' ')[0],
                    time4: this.getDateTime(1,-1,0).split(' ')[1],
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

		            var selectList = response.data.selectList;
					this.$data.options = selectList;
					this.$data.options2 = selectList;
		            this.$data.select_data1 = selectList[0].MONITOR_CON_PARA_ID;
		            this.$data.select_data2 = selectList[0].MONITOR_CON_PARA_ID;
		            this.initChart();
                })
        },
		methods: {
			initChart() {
				var mychart = echarts.init(this.$refs.myEchart);

				var url = "/monitor/getEcharts";
				var fromval = this.$data.form.date1+" "+this.$data.form.time1;
		        var toval = this.$data.form.date2+" "+this.$data.form.time2;
				var fromval1 = this.$data.form.date3+" "+this.$data.form.time3;
		        var toval1 = this.$data.form.date4+" "+this.$data.form.time4;
		        var params1 = new URLSearchParams();
				params1.append('from', fromval);
				params1.append('to', toval);
				params1.append('monitorId', this.$data.select_data1);
				var params2 = new URLSearchParams();
				params2.append('from', fromval1);
				params2.append('to', toval1);
				params2.append('monitorId', this.$data.select_data2);
				var nText = this.$data.options.find(item => item.MONITOR_CON_PARA_ID === this.$data.select_data1)['PARA_SERVICENAME']
		        this.$axios.all([
			    		this.$axios.post(url, params1),
			    		this.$axios.post(url, params2)
			 	]).then(this.$axios.spread(function (response1, response2) {

				    // 上面两个请求都完成后，才执行这个回调方法
				    console.log('response1', response1.data);
				    console.log('response2', response2.data);
	                var resName = response1.data.resName;
	                var xAxisList = response1.data.xAxisList;
	                var seriesList = response1.data.seriesList;
                    var resName1 = response2.data.resName;
                    var xAxisList1 = response2.data.xAxisList;
                    var seriesList1 = response2.data.seriesList;
                    var option = {
                                title : {
                                    text: nText
                                },
                                tooltip : {
                                    trigger: 'axis'
                                },
                                legend: {
                                    data:[resName,resName1]
                                },
                                toolbox: {
                                    show : true,
                                    feature : {
                                        mark : {show: true},
                                        dataView : {show: true, readOnly: false},
                                        magicType : {show: true, type: ['line', 'bar']},
                                        restore : {show: true},
                                        saveAsImage : {show: true}
                                    }
                                },
                                calculable : true,
                                xAxis : [
                                    {
                                        type : 'category',
                                        data : xAxisList
                                    }
                                ],
                                yAxis : [
                                    {
                                        type : 'value'
                                    }
                                ],
                                series : [
                                    {
                                        name:resName,
                                        type:'bar',
                                        data:seriesList,
                                        markPoint : {
                                            data : [
                                                {type : 'max', name: '最大值'},
                                                {type : 'min', name: '最小值'}
                                            ]
                                        },
                                        markLine : {
                                            data : [
                                                {type : 'average', name: '平均值'}
                                            ]
                                        }
                                    },
                                    {
                                        name:resName1,
                                        type:'bar',
                                        data:seriesList1,
                                        markPoint : {
//                                            data : [
//                                                {name : '年最高', value : 182.2, xAxis: 7, yAxis: 183, symbolSize:18},
//                                                {name : '年最低', value : 2.3, xAxis: 11, yAxis: 3}
//                                            ]
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
		                mychart.setOption(option);
				  }));
			},
			getDateTime(data,day,hour){
				  let myDate;
				  if(data !== 1){
				    myDate = new Date(data * 1000);
				  }else{
				    myDate = new Date();
				  }
				
				  let Y = myDate.getFullYear(),
				      M = myDate.getMonth() + 1,
				      D = myDate.getDate()+ day,
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