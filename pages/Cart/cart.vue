<template>
	<view class="container">
		<!-- 空白页 -->
		<view v-if="!hasLogin || empty===true" class="empty">
			<image src="/static/emptyCart.jpg" mode="aspectFit"></image>
			<view v-if="hasLogin" class="empty-tips">
				空空如也
				<navigator class="navigator" v-if="hasLogin" url="../index/index" open-type="switchTab">随便逛逛></navigator>index
			</view>
			<view v-else class="empty-tips">
				空空如也
				<view class="navigator" @click="navToLogin">去登陆></view>
			</view>
		</view>
		<view v-else>
			<view class="cart-list">
				<block v-for="(item, index) in cartList" :key="item.id">
					<view class="cart-item" :class="{'b-b': index!==cartList.length-1}">
						<view class="image-wrapper">
							<image :src="item.image"></image>
							<view class="checkbox" :class="{checked: item.checked}" @click="check('item', index)">
								<cover-image v-if="!item.checked" src="../../static/select.png"></cover-image>
								<cover-image v-else src="../../static/selected.png"></cover-image>
							</view>
						</view>
						<view class="item-right">
							<text class="clamp name">{{item.name}}</text>
							<text class="price">¥{{item.price}}</text>
							<uni-number-box 
							 class="step" 
						  	 :min="1" 
							 :max="item.stock" 
							 :value="item.number>item.stock?item.stock:item.number"
							 :isMax="item.number>=item.stock?true:false" 
							 :isMin="item.number===1" 
							 :index="index" 
							 @eventChange="numberChange"
							 >
							 </uni-number-box>
						</view>
						<text class="del-btn yticon icon-fork" @click="deleteCartItem(index)"></text>
					</view>
				</block>
			</view>

			<!-- 底部菜单栏 -->
			<view class="action-section">
				<view class="checkbox">
					<image :src="allChecked?'/static/selected.png':'/static/select.png'" mode="aspectFit" @click="check('all')"></image>
					<view class="clear-btn" :class="{show: allChecked}" @click="clearCart">
						清空
					</view>
				</view>
				<view class="total-box">
					<text class="price">¥{{total}}</text>
					<text class="coupon">
						已优惠
						<text>23.35</text>
						元
					</text>
				</view>
				<button type="primary" class="no-border confirm-btn" @click="createOrder">去结算</button>
			</view>
		</view>
	</view>
</template>

<script>
	import {
		mapState
	} from 'vuex';
	import uniNumberBox from '@/components/uni-number-box.vue'
	export default {
		components: {
			uniNumberBox
		},
		data() {
			return {
				total: 0, //总价格
				hasLogin: false,
				allChecked: false, //全选状态  true|false
				empty: false, //空白页现实  true|false
				cartList: [{
						"name": "Python基础教程",
						price: 46.50,
						"image": "../../static/img/book3.jpg",
						check: true,
						number: 1,
						stock: 12
					},
					{
						"name": "软件工程",
						price: 35.80,
						"image": "../../static/img/book4.jpg",
						check: true,
						number: 1,
						stock: 8
					},
					{
						"name": "Vue.js",
						price: 49.60,
						"image": "../../static/img/book5.jpg",
						check: true,
						number: 1,
						stock: 10
					},
					{
						"name": "求职开始",
						price: 36.30,
						"image": "../../static/img/book6.jpg",
						check: true,
						number: 1,
						stock: 9
					}
				]
			}
		},
		onLoad() {
			this.loadData();
		},
		updated:function(){
			this.hasLogin = uni.getStorageSync('login');
			// this.calcTotal();
		},
		methods: {
			navToLogin() {
				uni.navigateTo({
					url: '/pages/login/login'
				})
			},
			loadData() {
				this.hasLogin = uni.getStorageSync('login');
				this.calcTotal();
			},
			//选中状态处理
			check(type, index) {
				if (type === 'item') {
					this.cartList[index].checked = !this.cartList[index].checked;
				} 
				else {
					const checked = !this.allChecked
					const list = this.cartList;
					list.forEach(item => {
						item.checked = checked;
					})
					this.allChecked = checked;
				}
				this.calcTotal(type);
			},	
			//数量改变
			numberChange(data,index){
				this.cartList[data.index].number = data.number;
				// console.log(data.number);
				this.calcTotal();
			},
			//删除
			deleteCartItem(index) {
				uni.showModal({
					content: '是否删除该商品？',
					success: (e) => {
						if (e.confirm) {
							console.log('删除商品');
							let list = this.cartList;
							let row = list[index];
							let id = row.id;
							
							this.cartList.splice(index, 1);
							this.calcTotal();
							uni.hideLoading();
						}else if(e.cancel){
							console.log('取消删除');
						}
						
					}
				})
				
			},
			//清空
			clearCart() {
				uni.showModal({
					content: '清空购物车？',
					success: (e) => {
						if (e.confirm) {
							this.cartList = [];
						}
					}
				})
			},
			//计算总价
			calcTotal() {
				let list = this.cartList;
				if (list.length === 0) {
					this.empty = true;
					return;
				}
				let total = 0;
				let checked = true;
				list.forEach(item => {
					if (item.checked === true) {
						total += item.price * item.number;
					} else if (checked === true) {
						checked = false;
					}
				})
				this.allChecked = checked;
				this.total = Number(total.toFixed(2));
			},
		    createOrder(){
		    		uni.navigateTo({
		    			url: '../order/createOrder'
		    		})
		    }
		}
	}
</script>

<style lang='scss'>
	.container{
		padding-bottom: 134upx;
		
		/* 空白页 */
		.empty{
			position:fixed;
			left: 0;
			top:0;
			width: 100%;
			height: 100vh;
			padding-bottom:100upx;
			display:flex;
			justify-content: center;
			flex-direction: column;
			align-items:center;
			background: #fff;
			image{
				width: 240upx;
				height: 160upx;
				margin-bottom:30upx;
			}
			.empty-tips{
				display:flex;
				font-size: $font-sm+2upx;
				color: $font-color-disabled;
				.navigator{
					color: $uni-color-primary;
					margin-left: 16upx;
				}
			}
		}
	}
	.cart-list{
		display: flex;
		flex-direction:column;
		
	}
	/* 购物车列表项 */
	.cart-item{
		display:flex;
		position:relative;
		padding:30upx 40upx;
		.image-wrapper{
			width: 230upx;
			height: 230upx;
			flex-shrink: 0;
			position:relative;
			image {
				height: 230upx;
				width: 230upx;
				border-radius: 8upx;
			}
		}
		.checkbox{
			position:absolute;
			left:-16upx;
			top: -16upx;
			width: 50upx;
			z-index: 8;
			font-size: 44upx;
			line-height: 1;
			padding: 4upx;
			color: $font-color-disabled;
			background:#fff;
			border-radius: 50px;
		}
		.item-right{
			display:flex;
			flex-direction: column;
			flex: 1;
			overflow: hidden;
			position:relative;
			padding-left: 30upx;
			.title,.price{
				font-size:$font-base + 2upx;
				color: $font-color-dark;
				height: 40upx;
				line-height: 40upx;
			}
			.attr{
				font-size: $font-sm + 2upx;
				color: $font-color-light;
				height: 50upx;
				line-height: 50upx;
			}
			.price{
				height: 50upx;
				line-height:50upx;
			}
		}
		.del-btn{
			padding:4upx 10upx;
			font-size:34upx; 
			height: 50upx;
			color: $font-color-light;
		}
	}
	/* 底部栏 */
	.action-section{
		/* #ifdef H5 */
		margin-bottom:100upx;
		/* #endif */
		position:fixed;
		left: 30upx;
		bottom:30upx;
		z-index: 95;
		display: flex;
		align-items: center;
		width: 690upx;
		height: 100upx;
		padding: 0 30upx;
		background: rgba(255,255,255,.9);
		box-shadow: 0 0 20upx 0 rgba(0,0,0,.5);
		border-radius: 16upx;
		.checkbox{
			height:52upx;
			position:relative;
			image{
				width: 52upx;
				height: 100%;
				position:relative;
				z-index: 5;
			}
		}
		.clear-btn{
			position:absolute;
			left: 26upx;
			top: 0;
			z-index: 4;
			width: 0;
			height: 52upx;
			line-height: 52upx;
			padding-left: 38upx;
			font-size: $font-base;
			color: #fff;
			background: $font-color-disabled;
			border-radius:0 50px 50px 0;
			opacity: 0;
			transition: .2s;
			&.show{
				opacity: 1;
				width: 120upx;
			}
		}
		.total-box{
			flex: 1;
			display:flex;
			flex-direction: column;
			text-align:right;
			padding-right: 40upx;
			.price{
				font-size: $font-lg;
				color: $font-color-dark;
			}
			.coupon{
				font-size: $font-sm;
				color: $font-color-light;
				text{
					color: $font-color-dark;
				}
			}
		}
		.confirm-btn{
			padding: 0 38upx;
			margin: 0;
			border-radius: 100px;
			height: 76upx;
			line-height: 76upx;
			font-size: $font-base + 2upx;
			background: $uni-color-primary;
			box-shadow: 1px 2px 5px rgba(217, 60, 93, 0.72)
		}
	}
	/* 复选框选中状态 */
	.action-section .checkbox.checked,
	.cart-item .checkbox.checked{
		color: $uni-color-primary;
	}
</style>
