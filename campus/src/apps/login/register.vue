<template>
	<div class="login register">
		<div class="top-nav">
            <span @click="goBack">
                <i></i>
            </span>
            注册
        </div>
        <div class="login-min">
				<ul class="login-input">
					<li class="login-phone">
						<input type="text" name="" v-model="login.name" placeholder="请输入手机号码">
						<em @click="clearPhone"></em>
					</li>
					<li class="login-yan login-phone">
						<div class="">
						<input  type="text" name="" v-model="login.captcha" placeholder="请输入验证码">
						<em @click="clearYan"></em>
						</div>
						<button class="" @click="getYan" :disabled="yanDis" :class="yanDis?'disa':''">{{yanMsg}}</button>
					</li>
					<li class="login-pwd" v-show="pwd">
						<input type="password" name="" v-model="login.passwd" placeholder="请输入密码">
						<em @click="clearPwd"></em>
						<i @click="pwd=!pwd"></i>
					</li>
					<li class="login-pwd" v-show="!pwd">
						<input type="text" name="" v-model="login.passwd" placeholder="请输入密码">
						<em @click="clearPwd"></em>
						<i @click="pwd=!pwd" class="look"></i>
					</li>
				</ul>
				<p></p>
				<ul class="btn-list">
					<li>
						<button type='button' @click="Reset">注册</button>
					</li>
				</ul>
				<div style="margin-top:10px;">
					我已阅读并同意<span style="color:#ad65aa" @click="agreement">《宝贝儿服务协议》</span>		
				</div>
			</div>
	</div>
</template>
<script>
var verification=function(msg,reg){
    return reg.test(msg);
  };
 import Vue from 'vue';
   import ApiUtil from '../../js/utils/api-util.js';
import { Toast,Field,Popup,Picker,Indicator,Loadmore,MessageBox,DatetimePicker } from 'mint-ui';
  Vue.component(Popup.name,Popup);
  Vue.component(Picker.name,Picker);
   Vue.component(Loadmore.name, Loadmore);
   Vue.component(DatetimePicker.name, DatetimePicker);
	export default{
		data(){
			return{
				login:{
					name:'',
					passwd:'',
					captcha:''
				},
				yanMsg:'获取验证码',
				pwd:true,
				yanDis:true
			}
		},
		watch:{
			'login.name'(newVal){
				if(verification(newVal,/^1[3|4|5|7|8]\d{9}$/)&&this.yanMsg.indexOf('重新获取')==-1){
					this.yanDis=false;
				}
			}
		},
		created(){

		},
		methods:{
			agreement(){
				window.location.href="#/agreement";
			},
			Reset(){
				if(!this.login.name||!verification(this.login.name,/^1[3|4|5|7|8]\d{9}$/ )){
					Toast({
                        message: "请输入正确的手机号",
                        position: 'middle',
                        duration: 2000
                    })
				}else if(!this.login.passwd){
					Toast({
                        message: "请输入密码",
                        position: 'middle',
                        duration: 2000
                    })
				}else if(!this.login.captcha){
					Toast({
                        message: "请输入验证码",
                        position: 'middle',
                        duration: 2000
                    })
				}else{
					Indicator.open({
				        text: '获取中...',
				        spinnerType: 'fading-circle'
				      });
					let login={};
					login.type='1';
					login.mobile=this.login.name;
					login.captcha=this.login.captcha;
					login.passwd=md5(this.login.passwd);
					ApiUtil.getHttp('http://baby.api.cform.cn/api/user/register',login)
		                .then(
		                    (suc)=>{
		                         Indicator.close();
		                        if(suc.status==0){
		                            Toast({
	                                  message: '注册成功',
	                                  position: 'middle',
	                                  duration: 2000
	                                })
	                            	window.location.href="#/login?hide=1";
		                            // window.location.href="#/user_center";
		                            sessionStorage.setItem('back','');
		                        }else{
		                              Toast({
		                                  message: suc.errmsg,
		                                  position: 'middle',
		                                  duration: 2000
		                                })
		                        }
		                    },(err)=>{
		                    	 Indicator.close();
		                    	 // Toast({
		                      //             message: '服务器异常',
		                      //             position: 'middle',
		                      //             duration: 2000
		                      //           })
		                    }
		                )
	            }
			},
			getYan(){
				Indicator.open({
			        text: '获取中...',
			        spinnerType: 'fading-circle'
			      });
				let login={};
				login.type='1';
				login.mobile=this.login.name;
				let num=59;
				let me=this;
				me.yanMsg="重新获取(60s)";
				me.yanDis=true;
				var timer=setInterval(function() {
		            num--;
		            me.yanMsg="重新获取("+num+"s)";
		            me.yanDis=true;
		            if (num ==0){
						me.yanMsg='获取验证码';
		                clearInterval(timer);
		                if(verification(me.login.name,/^1[3|4|5|7|8]\d{9}$/)){
							me.yanDis=false;
						}else{
							 me.yanDis=false;
						}
		               
		            }
        		}, 1000);
				ApiUtil.getHttp('http://baby.api.cform.cn/api/sms/send',login)
	                .then(
	                    (suc)=>{
	                        console.log(suc)
	                        Indicator.close();
	                        if(suc.status==0){
	                             
	                        }else{
	                              Toast({
	                                  message: suc.errmsg,
	                                  position: 'middle',
	                                  duration: 2000
	                                })
	                        }
	                    },(err)=>{
	                    	Indicator.close();
	                    }
	                )
			},
			goBack(){
				history.go(-1);
			},
			clearPhone(){
				this.login.name="";
			},
			clearPwd(){
				this.login.passwd="";
			},
			clearYan(){
					this.login.captcha="";
			}
		}
	}
</script>
<style lang='scss'>
 @import './login';
</style>