<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 监控管理</el-breadcrumb-item>
                <el-breadcrumb-item>修改监控</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="form-box">
                <el-form ref="form" :model="form" label-width="150px">
                    <el-form-item label="监控名称">
                        <el-input v-model="form.PARA_SERVICENAME"></el-input>
                    </el-form-item>
                    <el-form-item label="监控阀值">
                        <el-input v-model="form.PARA_SERVICETHRESHOLD"></el-input>
                    </el-form-item>
                    <el-form-item label="告警手机">
                        <el-input v-model="form.PARA_NOTIFYMOBILENO"></el-input>
                    </el-form-item>
                    <el-form-item label="告警模版">
                        <el-input type="textarea" rows="5" v-model="form.PARA_NOTIFYMESSAGE"></el-input>
                    </el-form-item>
                    <el-form-item label="变量">
						<el-radio-group v-model="form.PARA_EXPRESSION">
                            <el-radio label="$1" value="$1"></el-radio>
                            <el-radio label="$2" value="$1"></el-radio>
                        </el-radio-group>                    
                    </el-form-item>
                    <el-form-item label="是否生效">
                        <el-radio-group v-model="form.PARA_EXPIRE">
                            <el-radio label="1" value="1">生效</el-radio>
                            <el-radio label="0" value="0">失效</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item label="执行间隔">
                        <el-radio-group v-model="form.MONITOR_SCHEDULE">
                            <el-radio label="5" value="5"></el-radio>
                            <el-radio label="10" value="5"></el-radio>
                            <el-radio label="15" value="15"></el-radio>
                            <el-radio label="30" value="30"></el-radio>
                            <el-radio label="60" value="60"></el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item label="非免打扰时间">
                        <el-select v-model="form.SEND_MSG_VALID_TIME" placeholder="请选择">
                            <el-option key="1" label="00:00~08:00" value="00:00~08:00"></el-option>
                            <el-option key="2" label="00:00~23:59" value="00:00~23:59"></el-option>
                            <el-option key="3" label="08:00~19:00" value="08:00~19:00"></el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item label="处理时间">
                        <el-col :span="11">
                            <el-date-picker type="date" placeholder="选择日期" v-model="form.date1" value-format="yyyy-MM-dd"  style="width: 100%;"></el-date-picker>
                        </el-col>
                        <el-col class="line" :span="2">-</el-col>
                        <el-col :span="11">
                            <el-time-picker placeholder="选择时间" v-model="form.date2" value-format="HH:mm:ss"  style="width: 100%;"></el-time-picker>
                        </el-col>
                    </el-form-item>
                    <el-form-item label="运算符">
                        <el-select v-model="form.OPERATOR_SYMBOL" placeholder="请选择">
                            <el-option key="1" label="大于" value=">"></el-option>
                            <el-option key="2" label="小于" value="<"></el-option>
                            <el-option key="3" label="大于等于" value=">="></el-option>
                            <el-option key="4" label="小于等于" value="<="></el-option>
                            <el-option key="5" label="等于" value="="></el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item label="是否环比">
                        <el-radio-group v-model="form.PRE_COMPARE_SWITCH">
                            <el-radio label="1" value="1">是</el-radio>
                            <el-radio label="0" value="0">否</el-radio>
                        </el-radio-group>
                    </el-form-item>
                     
                    <el-form-item label="监控SQL脚本">
                        <el-input type="textarea" rows="5" v-model="form.MAPITEM_VALUE"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="onSubmitEdit">表单提交</el-button>
                        <el-button>返回</el-button>
                    </el-form-item>
                </el-form>
            </div>
        </div>

    </div>
</template>

<script>
	import qs from 'qs';
    export default {
        components: {
            qs
        },data: function(){
            return {
                
                form: {
                		PARA_SERVICENAME : '服务接口调用量监控模版',
                    PARA_SERVICETHRESHOLD: '5',
                    PARA_NOTIFYMOBILENO: '15901044913',
                    PARA_NOTIFYMESSAGE: '【服务接口调用量监控】1分钟内超时3秒的条数为$1，超过阀值，请及时处理。',
                    PARA_EXPIRE:'1',
                    PARA_EXPRESSION: '$1',
                    MONITOR_SCHEDULE: '5',
                    
                    SEND_MSG_VALID_TIME: '08:00~19:00',
                    date1: this.getDateTime(1,0,0).split(' ')[0],
                    date2: this.getDateTime(1,0,0).split(' ')[1],
                    OPERATOR_SYMBOL: '>=',
                    PRE_COMPARE_SWITCH: '0',
                    MAPITEM_VALUE: 'SELECT FLOOR(RAND() * 100)'
                }
            }
        },
        activated: function() {  
		    this.queryData(this.$route.query.id);
		},
//      created(){
//    		this.queryData(this.$route.query.id);
//      },
        methods: {
            queryData(id) {
    			var url  = "/monitor/getMonitorsById";                
            // 发送请求:将数据返回到一个回到函数中
            // 并且响应成功以后会执行then方法中的回调函数
            var paramsChild = new URLSearchParams();
            paramsChild.append("id",id);
            this.$axios.post(url, paramsChild).then((response) => {
            			let res = response.data.res;
//          			alert(res);
            			if(res=='SUCCESS'){
		                this.$message.success('查询成功！');
		               	this.form.PARA_SERVICENAME=response.data.pd.PARA_SERVICENAME;
	                    this.form.PARA_SERVICETHRESHOLD=response.data.pd.PARA_SERVICETHRESHOLD;
	                    this.form.PARA_NOTIFYMOBILENO=response.data.pd.PARA_NOTIFYMOBILENO;
	                    this.form.PARA_NOTIFYMESSAGE=response.data.pd.PARA_NOTIFYMESSAGE;
	                    this.form.PARA_EXPIRE=response.data.pd.PARA_EXPIRE;
	                    this.form.PARA_EXPRESSION=response.data.pd.PARA_EXPRESSION;
	                    this.form.MONITOR_SCHEDULE=response.data.pd.MONITOR_SCHEDULE;
	                    
	                    this.form.SEND_MSG_VALID_TIME=response.data.pd.SEND_MSG_VALID_TIME;
//	                    alert(response.data.pd.DEAL_TIME);
	                    this.form.date1=response.data.pd.DEAL_TIME.split(' ')[0];
//	                    alert(response.data.pd.DEAL_TIME.split(' ')[0]);
//	                    alert(response.data.pd.DEAL_TIME.split(' ')[1]);
	                    this.form.date2=response.data.pd.DEAL_TIME.split(' ')[1];
	                    this.form.OPERATOR_SYMBOL=response.data.pd.OPERATOR_SYMBOL;
	                    this.form.PRE_COMPARE_SWITCH=response.data.pd.PRE_COMPARE_SWITCH;
	                    this.form.MAPITEM_VALUE=response.data.pd.MAPITEM_VALUE;
		                
            			}else{
		                this.$message.success('查询失败！');
            			}
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
			},
			onSubmitEdit(){
				let id = this.$route.query.id;
				var url  = "/monitor/updateMonitorById";                
            // 发送请求:将数据返回到一个回到函数中
            // 并且响应成功以后会执行then方法中的回调函数
            var paramsChild = new URLSearchParams();
            paramsChild.append("MONITOR_CON_PARA_ID",id);
            paramsChild.append("PARA_SERVICENAME",this.form.PARA_SERVICENAME);
            paramsChild.append("PARA_SERVICETHRESHOLD",this.form.PARA_SERVICETHRESHOLD);
            paramsChild.append("PARA_NOTIFYMOBILENO",this.form.PARA_NOTIFYMOBILENO);
            paramsChild.append("PARA_NOTIFYMESSAGE",this.form.PARA_NOTIFYMESSAGE);
            paramsChild.append("PARA_EXPIRE",this.form.PARA_EXPIRE);
            paramsChild.append("PARA_EXPRESSION",this.form.PARA_EXPRESSION);
            paramsChild.append("MONITOR_SCHEDULE",this.form.MONITOR_SCHEDULE);
            paramsChild.append("SEND_MSG_VALID_TIME",this.form.SEND_MSG_VALID_TIME);
            paramsChild.append("DEAL_TIME",this.form.date1+" "+this.form.date2);
            paramsChild.append("OPERATOR_SYMBOL",this.form.OPERATOR_SYMBOL);
            paramsChild.append("PRE_COMPARE_SWITCH",this.form.PRE_COMPARE_SWITCH);
            paramsChild.append("MAPITEM_VALUE",this.form.MAPITEM_VALUE);
            this.$axios.post(url, paramsChild).then((response) => {
            			let res = response.data.res;
            			if(res=='SUCCESS'){
		                this.$message.success('修改成功！');
		                this.$router.push({path: '/monitor'});
            			}else{
		                this.$message.success('修改失败！');
            			}
                })
			}
        }
    }
</script>