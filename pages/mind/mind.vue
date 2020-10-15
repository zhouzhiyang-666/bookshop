<template>
	<view class="content">
		<view class="header" @click="goLogin">
			<view class="userinfo">
				<view class="face">
					<image v-if="login" :src="avatarUrl"></image>
					<image v-else :src="missing_face"></image>
				</view>
				<view class="info">
					<view class="username">
						<text  class="uer-name">{{login?'尊敬的'+uerInfo.phoneData:'您未登录,点击此处登录'}}</text>
					</view>
					<view v-if="login" class="integral">积分:500</view>
					<view v-else class="integral">积分:0</view>
				</view>
			</view>
			<view class="setting"><image src="../../static/HM-PersonalCenter/setting.png"></image></view>
		</view>
		
		<view class="orders">
			<view class="box">
				<view class="label" v-for="(row,index) in orderTypeLise" :key="row.name" hover-class="hover"  @tap="toOrderType(index)">
					<view class="icon">
						<view class="badge" v-if="row.badge>0">
						{{row.badge}}
						</view>
						<image :src="'../../static/HM-PersonalCenter/'+row.icon"></image>
					</view>
					{{row.name}}
				</view>
			</view>
		</view>
		<view class="list" v-for="(list,list_i) in severList" :key="list_i">
			<view class="li" v-for="(li,li_i) in list" @tap="toPage(list_i,li_i)" v-bind:class="{'noborder':li_i==list.length-1}"  hover-class="hover" :key="li.name" >
				<view class="icon"><image :src="'../../static/HM-PersonalCenter/sever/'+li.icon"></image></view>
				<view class="text">{{li.name}}</view>
				<image class="to" src="../../static/HM-PersonalCenter/to.png"></image>
			</view>
		</view>
		<view v-if="login" class="list-cell log-out-btn" @click="login_out">
			<text class="cell-tit">退出登录</text>
		</view>
		<view v-else class="list-cell log-out-btn" @click="goLogin">
			<text class="cell-tit">请登录</text>
		</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				login:false,
				avatarUrl:'../../static/HM-PersonalCenter/face.jpeg',
				missing_face:'../../static/missing-face.png',
				uerInfo:{},
				orderTypeLise:[
					//name-标题 icon-图标 badge-角标
					{name:'待付款',icon:'l1.png',badge:0},
					{name:'待发货',icon:'l2.png',badge:3},
					{name:'待收货',icon:'l3.png',badge:5},
					{name:'评价',icon:'l4.png',badge:13},
					{name:'退款/售后',icon:'l5.png',badge:0}
				],
				severList:[
					[
						{name:'我的收藏',icon:'point.png'},
						{name:'优惠券',icon:'quan.png'},
						{name:'红包',icon:'momey.png'},
						{name:'任务',icon:'renw.png'},
					],
					[
						{name:'积分明细',icon:'mingxi.png'},
						{name:'抽奖',icon:'choujiang.png'},
						{name:'收货地址',icon:'addr.png',addr:'../address/address'},
						{name:'银行卡',icon:'bank.png'},
						{name:'安全中心',icon:'security.png'},
						{name:'在线客服',icon:'kefu.png'}
					]
				],
			};
		},
		onLoad() {
			this.load_Data();
		},
		updated:function(){
			this.load_Data();
		},
		methods: {
			load_Data(){
				try{
					console.log(uni.getStorageSync('login'));
					this.login=uni.getStorageSync('login');
					let User_info=this;
					uni.getStorage({
						key:'userInfo',
						success(e){
							console.log(e.data);
							User_info.user=e.data;
						}
						});
						this.uerInfo=User_info.user;
						console.log(this.uerInfo);
				}catch(e){
					//TODO handle the exception
					this.$api.msg(e.description);
				}
			},
			//登录
			goLogin() {				// this.login=uni.getStorageSync("login");				if (!this.login) {					console.log("点击前往登录");					uni.navigateTo({						url:'../login/login'					})				}			},
			//退出登录
			login_out(){				if(this.login){					var that=this;					uni.showModal({						title:'退出登录',						content:'是否退出登录',						success:function(res){							if(res.confirm){								console.log('用户点击确定');								that.login=false;
								try{
									uni.setStorageSync('login',false);
									// console.log(uni.getStorageSync('login'));
								}catch(e){
									//TODO handle the exception
									console.log(e.message);
								}															}							else if(res.cancel){								console.log('用户点击取消');							}						}					})				}			},
			//用户点击订单类型
			toOrderType(index){
				uni.showToast({title: this.orderTypeLise[index].name});
				uni.navigateTo({
					url:'../order/order'
				})
			},
			//用户点击列表项
			toPage(list_i,li_i){
				uni.showToast({
					title: this.severList[list_i][li_i].name,
					});
				try{
					if(this.severList[list_i][li_i].addr){
						uni.navigateTo({
							url:this.severList[list_i][li_i].addr
						});
					} 
				}catch(e){
					//TODO handle the exception
					console.log(e.description);
				}
			}
		}
	}
</script>

<style lang="scss">
.content{
	display: flex;
	flex-direction: column;
	flex: 1;
	width: 100%;
}

page{background-color:#fff}
.header{
	&.status{padding-top:var(--status-bar-height);}
	background-color:#55aaff;
	width:100%;height:30vw;
	padding:0 4%;
	display:flex;
	align-items:center;
	.userinfo{
		width:98%;display:flex;
		.face{flex-shrink:0;width:15vw;height:15vw;
			image{width:100%;height:100%;border-radius:100%}
		}
		.info{
			display:flex;flex-flow:wrap;padding-left:30upx;
			.username{width:100%;color:#fff;font-size:40upx}
			.integral{
				display:flex;
				align-items:center;
				padding:0 20upx;
				height:40upx;
				color:#fff;
				background-color:rgba(0,0,0,0.1);
				border-radius:20upx;
				font-size:24upx
				}
		}
	}
	.setting{
		flex-shrink:0;width:6vw;height:6vw;
		image{width:100%;height:100%}
	}
}
.list-cell{
		display:flex;
		align-items:baseline;
		padding: 30upx $page-row-spacing;
		line-height:30upx;
		position:relative;
		background: #aaffff;
		justify-content: center;
		&.log-out-btn{
			margin-top: 10upx;
			.cell-tit{
				color: $uni-color-primary;
				text-align: center;
			}
		}
	}
.hover{background-color:#eee}
.orders{
	background-color:#55aaff;
	width:100%;
	height:1vw;
	padding:0 4%;
	margin-bottom:calc(10vw + 50upx);
	display:inline-block;
	align-items:flex-start;
	border-radius:0 0 100% 100%;
	flex-direction: column;
	flex: 1;
	.box{
		width:98%;
		padding:0 1%;
		height:17vw;
		background-color:#ffffff;
		border-radius:24upx;
		box-shadow:0 0 20upx rgba(0,0,0,0.15);
		margin-bottom:20upx;
		display:flex;
		align-items:center;
		justify-content:center;
		.label{
			display:flex;
			align-items:center;
			justify-content:center;
			flex-flow:wrap;
			flex-direction: column;
			width:98%;
			height:16vw;
			color:#666666;
			font-size:26upx;
			.icon{
				position:relative;
				width:7vw;
				height:7vw;
				margin:0 1vw;
				.badge{
					position:absolute;
					width:4vw;
					height:4vw;
					background-color:#ec6d2c;
					top:-1vw;right:-1vw;
					border-radius:100%;
					font-size:20upx;
					color:#fff;
					display:flex;
					align-items:center;
					justify-content:center;
					z-index: 10;
					}
				image{width:7vw;height:7vw;z-index: 9;}
			}
		}
	}
}
.list{
	width:100%;
	margin-top: 0upx;
	flex-direction: column;
	border-bottom:solid 20upx #f1f1f1;
	.li{
		width:98%;
		height:100upx;
		padding:0 4%;
		border-bottom:solid 1upx #e7e7e7;
		display:flex;
		align-items:center;
	&.noborder{border-bottom:0}
		.icon{
			flex-shrink:0;
			width:50upx;
			height:50upx;
			image{width:50upx;height:50upx}
		}
		.text{padding-left:20upx;width:100%;color:#666}
		.to{flex-shrink:0;width:40upx;height:40upx}
	}
}
</style>
