<template>
	<view class="home">
		<view class="head-first">
			<view class="winfo">
				<span class="font">{{time}}好</span> <span class="nick"> {{nickName}} </span>
			</view>
			<uni-search-bar placeholder="输入商品名称" class="search" radius="18" @confirm="search" v-model="search_val">
				<uni-icons slot="searchIcon" color="#999999" size="18" type="home" />
			</uni-search-bar>
		</view>
		<view class="sclo">
			<swiper :indicator-dots="true" :autoplay="true" class="swiper" circular="true">
				<swiper-item v-for="banner in banners" :key="banner.pid" @click="sendinfo(banner)">
					<image :src="banner.bannerImg" :alt="banner.name" class="banner-image" mode="widthFix"></image>
					<view class="swiper-name">
						{{banner.name}}
					</view>
				</swiper-item>
			</swiper>
		</view>
		<view class="hot">
			<view class="card">
				<view class="text">
					<span class="text-s">热卖推荐</span>
				</view>
			</view>
			<view class="recommend-content">
				<view class="content-items" v-for="val in hotgoods" @click="sendinfo(val)">
					<view class="items-pic">
						<image :src="val.smallImg" mode="widthFix" class="pic-show"></image>
						<span class="pit-hot">hot</span>
					</view>
					<view class="zn-name">{{val.name}}</view>
					<view class="en-name">{{val.enname}}</view>
					<view class="price">￥{{val.price}}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		onShow() {
			uni.request({
				url: 'http://www.kangliuyong.com:10002/banner', //仅为示例，并非真实接口地址。
				data: {
					appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
				},
				success: (res) => {
					if (res.data.code === 300) {
						this.banners = res.data.result
					}
				}
			});
			this.getuser();
			let date = new Date();
			if (date.getHours() >= 6 && date.getHours() < 10) {
				this.time = "早上"
			} else if (date.getHours() >= 10 && date.getHours() < 14) {
				this.time = "中午"
			} else if (date.getHours() >= 14 && date.getHours() < 18) {
				this.time = "下午"
			} else if (date.getHours() >= 18 && date.getHours() < 23) {
				this.time = "晚上"
			} else {
				this.time = "熬夜了"
			}
			this.gethots();
		},
		data() {
			return {
				nickName: "",
				time: "",
				banners: [],
				search_val: "",
				hotgoods: [],
				index: 0,
				info: {}
			}
		},
		methods: {
			getuser() {
				uni.request({
					url: 'http://www.kangliuyong.com:10002/findMy', //仅为示例，并非真实接口地址。
					data: {
						appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
						tokenString: uni.getStorageSync('token')
					},
					success: (res) => {
						if (res.data.code === 'A001') {
							this.nickName = res.data.result[0].nickName
						} else if (res.data.code === 700) {
							this.nickName = "访客用户"
						}
					}
				});
			},
			search() {
				uni.setStorageSync('search_val', this.search_val)
				uni.switchTab({
					url: '/pages/menu1/menu1'
				});
			},
			gethots() {
				uni.request({
					url: 'http://www.kangliuyong.com:10002/typeProducts', //仅为示例，并非真实接口地址。
					method: "GET",
					data: {
						appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
						key: "isHot",
						value: 1
					},
					success: (res) => {
						if (res.data.code === 500) {
							this.hotgoods = res.data.result
						}
					}
				});
			},
			sendinfo(val) {
				uni.setStorageSync('infomation', val)
				//在起始页面跳转到test.vue页面并传递参数
				uni.navigateTo({
					url: '../infomation/infomation'
				});
			}
		},
	}
</script>

<style lang="scss">
	.head-first {
		width: 100%;
		display: flex;
		justify-content: space-between;
		/* padding: 3px; */
		background-color: #fff;
		border-bottom: 1px solid #999;

		.winfo {
			margin-top: 15px;
			margin-left: 5px;
			font-weight: bold;

			.font {
				/* font-weight: bold; */
				color: #8f8b8b;
			}

			.nick {
				color: #0c34ba;
				font-size: 8px;
				/* margin-bottom: px; */
				width: 60px;
				white-space: nowrap;
				overflow: hidden;
				text-overflow: ellipsis !important;
			}
		}

		.search {
			/* width: 60%; */
			flex: 1;
		}

		::v-deep .uni-searchbar__box {
			justify-content: flex-start;

			.uni-searchbar__box-icon-search {
				padding: 0 0 0 8px;

				.uni-icons {
					color: #0c34ba !important;
				}
			}
		}
	}

	.sclo {
		display: flex;
		justify-content: space-between;
		padding: 10px;

		.swiper {
			width: 100%;
			height: 280px;
			position: relative;

		}

		::v-deep .uni-swiper-dots {
			left: 83%;
			padding-bottom: 5px;
		}

		.banner-image {
			width: 100%;
			height: 100%;
			display: flex;
			position: absolute;
			z-index: -1;
			object-fit: cover;
			border-radius: 10px;
		}

		.swiper-name {
			position: absolute;
			text-align: center;
			margin-top: 240px;
			margin-left: 10px;
			padding: 4px 8px;
			color: white;
			border-radius: 45px;
			background-color: #0c34ba;
		}
	}

	.hot {
		padding: 10px;

		.card {
			display: flex;
			height: 50px;
			margin-bottom: 10px;
			position: sticky;
			top: 0;
			z-index: 999;
			background-color: #fff;

			.text {
				width: 40%;
				height: 40px;
				margin-top: 10px;
				display: flex;
				justify-content: center;
				border-radius: 0 20px 0 0;
				align-items: center;
				color: aliceblue;
				font-weight: bold;
				background-color: #0c34ba;
			}
		}

		.recommend-content {
			display: flex;
			justify-content: space-between;
			flex-wrap: wrap;

			.content-items {
				width: 42%;
				padding: 10px;
				margin-top: 10px;
				background-color: #fff;

				.items-pic {
					width: 160px;
					height: 160px;
					position: relative;

					.pic-show {
						width: 100%;
						position: absolute;
						border-radius: 10px;
						z-index: 1;
					}

					.pit-hot {
						width: 26px;
						height: 24px;
						color: white;
						margin-left: 10px;
						text-align: center;
						background-color: #0c34ba;
						z-index: 1;
						position: absolute;
					}
				}

				.zn-name {
					font-weight: bold;
					font-size: 18px;
					color: #5f6366;
				}

				.en-name {
					font-size: 12px;
					white-space: nowrap;
					overflow: hidden;
					text-overflow: ellipsis;
					color: #5f6366;
				}

				.price {
					font-weight: bold;
					color: #0c34ba;
				}
			}
		}
	}

	::v-deep .uni-swiper-dot {
		width: 24px;
		border-radius: 2px;
		background-color: #5e5eba;
	}

	::v-deep.uni-swiper-dot-active {
		background-color: #0c34ba;
	}
</style>