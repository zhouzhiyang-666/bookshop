<template>
	<view class="content">
		
		<!-- 小程序头部兼容 -->
		<!-- #ifdef MP -->
		<view class="mp-search-box">
			<input class="ser-input" type="text" value="输入关键字搜索" disabled @click="gotoSearch"/>
		</view>
		<!-- #endif -->
		<!-- 头部轮播 -->
		<view class="carousel-section">
			<!-- 标题栏和状态栏占位符 -->
		<view class="titleNview-placing"></view>
		<!-- 背景色区域 -->
		<view class="titleNview-background" :style="{backgroundColor:titleNViewBackground}"></view>
		<swiper class="carousel" circular @change="swiperChange">
			<swiper-item v-for="(item, index) in carouselList" :key="index" class="carousel-item" @click="navToDetailPage({name: '轮播广告'})">
				<image :src="item.imgsrc" />
			</swiper-item>
		</swiper>
		<!-- 自定义swiper指示器 -->
		<view class="swiper-dots">
			<text class="num">{{swiperCurrent+1}}</text>
			<text class="sign">/</text>
			<text class="num">3</text>
		</view>
		</view>
		<view class="adv">
			<image class="adv_img" src="../../static/temp/ad1.jpg" title="广告" alt="sss" ></image>
		</view>
		<view class="goods-list">
			<view 
				v-for="(item, index) in goodsList" :key="index"
				class="goods-item"
				@click="navToDetailPage(item)"
			>
				<view class="image-wrapper">
					<image :src="item.image" mode="aspectFill"></image>
				</view>
				<text class="title clamp">{{item.name}}</text>
				<view class="price-box">
					<text class="price">{{item.price}}</text>
					<text>已售 {{item.sold}}</text>
				</view>
			</view>
			<!-- <view :class="show? active:hide">正在加载...</view> -->
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				titleNViewBackground: '',
				swiperCurrent: 0,
				news:[],
				show:false,
				active:`{
					display: flex;
					background-color: #AAAAAA;
				}`,
				hide:`{
					display: none;
				}`,
				goodsList:[
					{"name":"羊毛卷",price:53.20,"image":"../../static/img/book0.jpg",sold:50},
					{"name":"高等数学",price:47.60,"image":"../../static/img/book1.jpg",sold:34},
					{"name":"计算机网络技术",price:38.60,"image":"../../static/img/book2.jpg",sold:23},
					{"name":"Python基础教程",price:46.50,"image":"../../static/img/book3.jpg",sold:55},
					{"name":"软件工程",price:35.80,"image":"../../static/img/book4.jpg",sold:68},
					{"name":"Vue.js",price:49.60,"image":"../../static/img/book5.jpg",sold:92},
					{"name":"求职开始",price:36.30,"image":"../../static/img/book6.jpg",sold:65},
					{"name":"博弈论",price:49.00,"image":"http://img3m2.ddimg.cn/53/28/28556702-1_u_6.jpg",sold:86},
					{"name":"普京大传",price:49.80,"image":"http://img3m7.ddimg.cn/58/35/28527997-1_u_6.jpg",sold:35},
					{"name":"刘慈欣科幻作品典藏套装",price:216.80,"image":"http://img3m7.ddimg.cn/55/25/25353757-1_u_2.jpg",sold:102}
				],
				carouselList: [
					{imgsrc:'http://img60.ddimg.cn/upload_img/00316/by/750x315-1592818924.jpg',background:'rgb(170, 255, 255)'},
					{imgsrc:'http://img63.ddimg.cn/upload_img/00822/cxtc/750x315---1592550969.jpg',background:'rgb(85, 170, 255)'},
					{imgsrc:'http://img61.ddimg.cn/upload_img/00725/1/FHC750x315jf-1592559353.jpg',background:'rgb(255, 0, 127)'}
				]	
			}
		},
		onLoad() {
			// uni.request({
			// 	url: 'https://unidemo.dcloud.net.cn/api/news',
			// 	method: 'GET',
			// 	data: {},
			// 	success: res => {
			// 		console.log(res.data);
			// 		this.news=res.data;
			// 	},
			// 	fail: () => {},
			// 	complete: () => {}
			// });
		},
		methods: {
			//轮播图切换修改背景色
			swiperChange(e) {
				const index = e.detail.current;
				this.swiperCurrent = index;
				this.titleNViewBackground = this.carouselList[index].background;
			},
			//详情页
			navToDetailPage(item) {
				//测试数据没有写id，用name代替
				let id = item.name;
				uni.navigateTo({
					url: '../product/product?id='+id
				})
			},
			// #ifndef MP
			// 标题栏input搜索框点击
			gotoSearch() {
				this.$api.msg('点击了搜索框');
				uni.navigateTo({
					url: '/pages/search/search'
				})
			},
			// onNavigationBarSearchInputClicked: function(e) {
			// 	this.$api.msg('点击了搜索框');
			// },
			//点击导航栏 buttons 时触发
			onNavigationBarButtonTap(e) {
				const index = e.index;
				if (index === 0) {
					this.$api.msg('点击了扫描');
				} else if (index === 1) {
					// #ifdef APP-PLUS
					const pages = getCurrentPages();
					const page = pages[pages.length - 1];
					const currentWebview = page.$getAppWebview();
					currentWebview.hideTitleNViewButtonRedDot({
						index
					});
					// #endif
					this.$api.msg('点击了通知');
					uni.navigateTo({
						url: '/pages/notice/notice'
					})
				}
			},
			// #endif
			lazyLoading (event) { 
			        		// 滚动到底部，再加载的处理事件
			        		// 获取 可视区高度`、`滚动高度`、`页面高度`
							  // scrollTop为滚动条在Y轴上的滚动距离。        
							  // clientHeight为内容可视区域的高度。        
							  // scrollHeight为内容可视区域的高度加上溢出（滚动）的距离。
				            let scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
				            let clientHeight = document.documentElement.clientHeight;
				            let scrollHeight = document.documentElement.scrollHeight;
				            if (scrollTop + clientHeight >= scrollHeight) { // 滚动到底部，逻辑代码
				                //事件处理
				                console.log("滚动到底部，触发")//此处可以添加数据请求
				                // 这这里可以写一些业务逻辑，请求数据等
								// var random = new Array(0,1,2,3,4,5,6,7);
								// var index1 = Math.ranint(0,7);
								// var index2 = Math.ranint(0,7);
								var index1 = parseInt(Math.random()*8,10); ; 
								var index2 = parseInt(Math.random()*8,10); ; 
								// console.log(index1,index2);
								// this.show = true;
								// setTimeout(function(){
								this.goodsList.push(this.goodsList[index1]);
								this.goodsList.push(this.goodsList[index2]);
								// },1000);
								// this.show = false;
								
			                }
			}
		},
		// 这里呢首先在methods里面写好一个方法，最后通过`window.addEventListener`去调用
		created()
		{
			window.addEventListener('scroll', this.lazyLoading); // 滚动到底部，再加载的处理事件
		},
		//页面离开后销毁，防止切换路由后上一个页面监听scroll滚动事件会在新页面报错问题 
		destroyed() { 
			window.removeEventListener('scroll', this.lazyLoading)
			//页面离开后销毁，防止切换路由后上一个页面监听scroll滚动事件会在新页面报错问题 
		}
	}
</script>

<style lang="scss">
// .uni-media-list-body{height: auto;}
// .uni-media-list-text-top{line-height:1.6em;}

.mp-search-box{
		position:absolute;
		left: 0;
		top: 30upx;
		z-index: 9999;
		width: 100%;
		padding: 0 80upx;
		.ser-input{
			flex:1;
			height: 56upx;
			line-height: 56upx;
			text-align: center;
			font-size: 28upx;
			color:$font-color-base;
			border-radius: 20px;
			background: rgba(255, 85, 0, 153);
		}
	}
page, .content{
		background: $page-color-base;
	}
	.content{
		padding-top: 96upx;
		flex-direction: column;
	}
	
/* 头部 轮播图 */
	.carousel-section {
		position: relative;
		padding-top: 10px;
		height: 450upx;
		width: 100%;
		// .titleNview-placing {
		// 	height: var(--status-bar-height);
		// 	padding-top: 44px;
		// 	box-sizing: content-box;
		// }
		.titleNview-background {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 426upx;
			transition: .4s;
		}
	}
	
//轮播图样式
.carousel {
		width: 100%;
		height: 350upx;

		.carousel-item {
			width: 100%;
			height: 100%;
			padding: 0 28upx;
			overflow: hidden;
		}

		image {
			width: 100%;
			height: 100%;
			border-radius: 10upx;
		}
	}
.swiper-dots {
		display: flex;
		position: absolute;
		left: 60upx;
		bottom: 15upx;
		width: 72upx;
		height: 36upx;
		background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAABkCAYAAADDhn8LAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTMyIDc5LjE1OTI4NCwgMjAxNi8wNC8xOS0xMzoxMzo0MCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6OTk4MzlBNjE0NjU1MTFFOUExNjRFQ0I3RTQ0NEExQjMiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6OTk4MzlBNjA0NjU1MTFFOUExNjRFQ0I3RTQ0NEExQjMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6Q0E3RUNERkE0NjExMTFFOTg5NzI4MTM2Rjg0OUQwOEUiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6Q0E3RUNERkI0NjExMTFFOTg5NzI4MTM2Rjg0OUQwOEUiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz4Gh5BPAAACTUlEQVR42uzcQW7jQAwFUdN306l1uWwNww5kqdsmm6/2MwtVCp8CosQtP9vg/2+/gY+DRAMBgqnjIp2PaCxCLLldpPARRIiFj1yBbMV+cHZh9PURRLQNhY8kgWyL/WDtwujjI8hoE8rKLqb5CDJaRMJHokC6yKgSCR9JAukmokIknCQJpLOIrJFwMsBJELFcKHwM9BFkLBMKFxNcBCHlQ+FhoocgpVwwnv0Xn30QBJGMC0QcaBVJiAMiec/dcwKuL4j1QMsVCXFAJE4s4NQA3K/8Y6DzO4g40P7UcmIBJxbEesCKWBDg8wWxHrAiFgT4fEGsB/CwIhYE+AeBAAdPLOcV8HRmWRDAiQVcO7GcV8CLM8uCAE4sQCDAlHcQ7x+ABQEEAggEEAggEEAggEAAgQACASAQQCCAQACBAAIBBAIIBBAIIBBAIABe4e9iAe/xd7EAJxYgEGDeO4j3EODp/cOCAE4sYMyJ5cwCHs4rCwI4sYBxJ5YzC84rCwKcXxArAuthQYDzC2JF0H49LAhwYUGsCFqvx5EF2T07dMaJBetx4cRyaqFtHJ8EIhK0i8OJBQxcECuCVutxJhCRoE0cZwMRyRcFefa/ffZBVPogePihhyCnbBhcfMFFEFM+DD4m+ghSlgmDkwlOgpAl4+BkkJMgZdk4+EgaSCcpVX7bmY9kgXQQU+1TgE0c+QJZUUz1b2T4SBbIKmJW+3iMj2SBVBWz+leVfCQLpIqYbp8b85EskIxyfIOfK5Sf+wiCRJEsllQ+oqEkQfBxmD8BBgA5hVjXyrBNUQAAAABJRU5ErkJggg==);
		background-size: 100% 100%;

		.num {
			width: 36upx;
			height: 36upx;
			border-radius: 50px;
			font-size: 24upx;
			color: #fff;
			text-align: center;
			line-height: 36upx;
		}

		.sign {
			position: absolute;
			top: 0;
			left: 50%;
			line-height: 36upx;
			font-size: 12upx;
			color: #fff;
			transform: translateX(-50%);
		}
	}

.adv{
	display: flex;
	flex-direction: column;
	height: 100upx;
	width: 100%;
	line-height: 70upx;
	text-align: center;
	align-items: center;
	margin: 2upx auto;
	.adv_img{
		width: 100%;
		// background-size: cover;
	}
}

//商品列表样式
.goods-list{
	
		display:flex;
		flex-wrap:wrap;
		padding: 0 30upx;
		background: #fff;
		.goods-item{
			display:flex;
			flex-direction: column;
			width: 48%;
			padding-bottom: 40upx;
			&:nth-child(2n+1){
				margin-right: 4%;
			}
		}
		.image-wrapper{
			width: 100%;
			height: 330upx;
			border-radius: 3px;
			overflow: hidden;
			image{
				width: 100%;
				height: 100%;
				opacity: 1;
			}
		}
		.title{
			font-size: $font-lg;
			color: $font-color-dark;
			line-height: 80upx;
		}
		.price-box{
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding-right: 10upx;
			font-size: 24upx;
			color: $font-color-light;
		}
		.price{
			font-size: $font-lg;
			color: $uni-color-primary;
			line-height: 1;
			&:before{
				content: '￥';
				font-size: 26upx;
			}
		}
	
}
</style>
