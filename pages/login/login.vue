<template>
	<view class="login">
		<view class="content">
			<!-- 头部logo -->
			<view class="header">
				<image :src="logoImage"></image>
			</view>
			<!-- 主体表单 -->
			<view class="main">
				<wInput
					v-model="phoneData"
					type="text"
					maxlength="11"
					placeholder="用户名/电话"
				></wInput>
				<wInput
					v-model="passData"
					type="password"
					maxlength="11"
					placeholder="密码"
				></wInput>
			</view>
			<wButton 
				text="登 录"
				:rotate="isRotate" 
				@click.native="startLogin()"
				class="wbutton"
			></wButton>
			
			<!-- 其他登录 -->
			<view class="other_login cuIcon">
				<view class="login_icon">
					<view class="cuIcon-weixin" @tap="login_weixin"></view>
				</view>
				<view class="login_icon">
					<view class="cuIcon-weibo" @tap="login_weibo"></view>
				</view>
				<view class="login_icon">
					<view class="cuIcon-github" @tap="login_github"></view>
				</view>
			</view>
			
			<!-- 底部信息 -->
			<view class="footer">
				<navigator url="./forget" open-type="navigate">找回密码</navigator>
				<text>|</text>
				<navigator url="./register" open-type="navigate">注册账号</navigator>
			</view>
		</view>
	</view>
</template>

<script>
	var _this;
	import wInput from '../../components/watch-login/watch-input.vue' //input
	import wButton from '../../components/watch-login/watch-button.vue' //button
	
	export default {
		data() {
			return {
				//logo图片 base64
				logoImage: '../../static/uni-center/logo.png',
				phoneData:'', //用户/电话
				passData:'', //密码
				isRotate: false, //是否加载旋转
			};
		},
		components:{
			wInput,
			wButton,
		},
		mounted() {
			_this= this;
			//this.isLogin();
		},
		methods: {
			isLogin(){
				//判断缓存中是否登录过，直接登录
				// try {
				// 	const value = uni.getStorageSync('setUserData');
				// 	if (value) {
				// 		//有登录信息
				// 		console.log("已登录用户：",value);
				// 		_this.$store.dispatch("setUserData",value); //存入状态
				// 		uni.reLaunch({
				// 			url: '../../../pages/index',
				// 		});
				// 	}
				// } catch (e) {
				// 	// error
				// }
			},
		    startLogin(){
				//登录
				if(this.isRotate){
					//判断是否加载中，避免重复点击请求
					return false;
				}
				if (this.phoneData.length == "") {
					_this.$api.msg("用户名不能为空!");
				    return;
				}
		        if (this.passData.length < 5) {
					_this.$api.msg("密码不正确!");
		            return;
		        }
				
				let UData=this;
				try{
					uni.getStorage({
						key:'userInfo',
						success(e){
							// console.log(e.data);     //打印测试数据
							UData.user=e.data;
						}
						});
					// console.log(JSON.parse(UData.data.phoneData));
					
					if(UData.user.phoneData==this.phoneData&&UData.user.passData==this.passData){
					 	console.log("登录成功");
					
						uni.setStorageSync('login',true);
						_this.$api.msg("登陆成功!");
						setTimeout(function(){
							uni.reLaunch({
							    url:'../mind/mind'
							});
						},2000);
					}
					    else{
							_this.$api.msg("账号或密码错误!");
					}
					
				}catch(e){
					//TODO handle the exception
					console.log(e.message);
				}
				finally{
					_this.isRotate=true;
					setTimeout(function(){
						_this.isRotate=false
					},3000);
				}
				
				
				// getLogin(){
				// 		let UData=uni.getStorage("userInfo");
				// 		if(UData.data.phoneData==this.phoneData&&UData.data.passData==this.passData){
				// 			return true;
				// 		}
				// }
				// .then(res => {
				// 	console.log(res)
				// 	//简单验证下登录（不安全）
				// 	if(_this.phoneData==res.data.username && _this.passData==res.data.password){
				// 		let userdata={
				// 			"username":res.data.username,
				// 			"nickname":res.data.nickname,
				// 			"accesstoken":res.data.accesstoken,
				// 		} //保存用户信息和accesstoken
				// 		_this.$store.dispatch("setUserData",userdata); //存入状态
				// 		try {
				// 			uni.setStorageSync('setUserData', userdata); //存入缓存
				// 		} catch (e) {
				// 			// error
				// 		}
				// 		uni.showToast({
				// 			icon: 'success',
				// 			position: 'bottom',
				// 			title: '登录成功'
				// 		});
				// 		uni.reLaunch({
				// 			url: '../index/index',
				// 		});
				// 	}else{
				// 		_this.passData=""
				// 		uni.showToast({
				// 			icon: 'error',
				// 			position: 'bottom',
				// 			title: '账号或密码错误，账号admin密码admin'
				// 		});
				// 	}
				// 	uni.hideLoading();
				// }).catch(err => {
				// 	uni.hideLoading();
				// })
				
		    },
			login_weixin() {
				//微信登录
				_this.$api.msg("微信登录");
			},
			login_weibo() {
				//微博登录
				_this.$api.msg("微博登录");
			},
			login_github() {
				//github登录
				_this.$api.msg("github登录");
			}
		}
	}
</script>

<style>
	@import url("../../components/watch-login/css/icon.css");
	@import url("./css/main.css");
</style>
