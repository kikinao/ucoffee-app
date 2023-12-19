<template>
	<view class="pay">
		<view class="goback" @click='gotoback'>
			<uni-nav-bar fixed left-icon="left" left-text="返回" title="订单结算"></uni-nav-bar>
		</view>

		<!-- 地址 -->
		<view class="address">
			<view class="choose-address">

				<text>选择地址</text>
				<uni-icons type="forward" size="18"></uni-icons>



			</view>
			<view class="user-msg">
				<text class="name">周</text>
				<text class="phone">17385372213</text>
				<text class="default">默认</text>

			</view>
			<view class="address-detail">广州省广东市</view>
		</view>



		<!-- 订单信息 -->
		<view class="order-box">
			<view class="title">订单信息</view>

			<view class="content">

				<!-- 选择 -->
				<view class="car-item" v-for="(item,index) in productData" :key="item.sid">
					<!-- <view class="choose">
						<label class="radio">
							<radio value="r1" :checked="item.choose" @click="changeChoose(index)" />
						</label>
					</view> -->
					<!-- 图片 -->
					<view class="product-item">
						<image :src="item.small_img" mode=""></image>
					</view>
					<!-- 产品信息 -->
					<view class="car-msg">
						<view class="name-rules">
							<text>{{item.name}}</text>
							<text>{{item.rule}}</text>
						</view>

						<view class="enname">{{item.enname}}</view>

						<view class="price-step">
							<text>￥{{item.price}}</text>
							<text>x{{item.count}}</text>

							<!-- <uni-number-box background="#ffffff" :min="1" v-model="item.count"></uni-number-box> -->
						</view>
					</view>

				</view>
				<!-- 时间 合计多少件和总价格 -->

				<view class="time-box">
					<view class="tiem">{{time}}</view>
					<view class="count-price">
						<text class="count">共计{{count}}件</text>
						<view class="price">合计<text>￥{{allPrice}}</text></view>
					</view>
				</view>
			</view>




		</view>


		<!-- 结算按钮 -->
		<view class="footer-box">
			<view class="btn" @click="buy">立即结算</view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				productData: [], // 添加初始值
				token: "",
				sids: [],
				time: "",
				count: "",
				allPrice: 0
			}
		},

		//生命周期
		onLoad(options) {
			console.log("options", options)
			this.sids = options.sids

		},
		onShow() {
			//获取token
			this.token = uni.getStorageSync("token")
			this.getPay()
		},

		methods: {
			gotoback() {
				uni.switchTab({
					url: '../cart/cart'
				})
			},
			buy() {

				uni.request({
					// 路径
					url: "http://www.kangliuyong.com:10002/pay",
					// 请求类似如果是get可以不用写的
					// 参数
					method: "POST",

					data: {
						appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
						tokenString: uni.getStorageSync('token'),
						sids: this.sids,
						phone: '17385372213',
						address: "贵州毕节",
						receiver: "周"

					},

					header: {
						'content-type': 'application/x-www-form-urlencoded;charset=utf-8' // 默认值
					},
					// 成功时候执行的
					success: (res) => {
						console.log('res', res)
						//this.bannerData = res.data.result

						if (res.data) {
							//跳转订单页面
							uni.redirectTo({
								url: "/pages/myorder/myorder"
							})

						} else {
							uni.showToast({
								title: res.data.msg,
								icon: 'error'
							})
						}
					}
				})

			},



			computer() {
				this.productData.map(item => {
					this.count += item.count;
					this.allPrice += item.count * item.price
				})

				// 处理时间 获取电脑的年月份
				let date = new Date();

				let yeary = date.getFullYear();
				// 0-11 所有要加1
				let month = date.getMonth() + 1
				let day = date.getDate();

				let hours = date.getHours();

				let minutes = date.getMinutes()

				let seconds = date.getSeconds();

				this.time = `${yeary}-${month}-${day} ${hours}:${minutes}:${seconds}`

			},





			getPay() {
				//获取我们要下单的信息
				if (!this.token) {
					uni.navigateTo({
						url: "/pages/login/login"
					})
					return
				}

				// 获取后端数据的方式
				uni.request({
					// 路径
					url: "http://www.kangliuyong.com:10002/commitShopcart",
					// 请求类似如果是get可以不用写的
					// 参数
					data: {
						appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
						tokenString: uni.getStorageSync('token'),
						sids: this.sids
					},
					// 成功时候执行的
					success: (res) => {
						console.log('res', res)
						this.productData = res.data.result

						if (Array.isArray(this.productData) && this.productData.length > 0) {
							this.computer();
						}
					}
				})
			}



		}

	}
</script>

<style lang="scss">
	.pay {
		.uni-navbar-btn-text {
			span {
				color: #173dbd;
			}
		}

		.uni-navbar__content_view {
			.uni-icons {
				color: #173dbd !important;
			}
		}

		.address {
			background: white;
			margin: 30rpx;
			border-radius: 20rpx;
			padding: 20rpx;

			.choose-address {
				display: flex;
				align-items: center;
				font-size: 28rpx;
				color: #646566;

				.uni-icons {
					color: #173dbd !important;
				}
			}

			.user-msg {
				margin: 30rpx 0;

				.name {
					color: #173dbd;
					font-weight: 600;
				}

				.phone {
					margin: 0 30rpx;
					color: #818195;
				}

				.default {
					background: #173dbd;
					color: white;
					padding: 5rpx 20rpx;
					border-radius: 30rpx;
					font-size: 28rpx;
				}

				.address-detail {
					color: #818195;
					font-size: 28rpx;
				}
			}
		}

		.order-box {
			background: white;
			margin: 30rpx;
			padding: 20rpx;
			border-radius: 10rpx;
			position: relative;

			.title {
				color: #818195;
			}

			.content {
				padding-bottom: 200rpx;
				margin: 20rpx;

				.car-item {
					margin-top: 30rpx;
					height: 200rpx;
					background: white;
					border-radius: 20rpx;
					display: flex;
					align-items: center;

					.product-item {
						image {
							margin-right: 10rpx;
							width: 180rpx;
							height: 180rpx;
						}
					}

					.car-msg {
						height: 170rpx;
						flex: 1;
						padding-right: 30rpx;

						.name-rules {
							text: first-child {
								color: #646566;
								font-size: 28rpx;
							}

							text:nth-child(2) {
								margin-left: 30rpx;
								color: #a7a7a7;
								font-size: 26rpx;
							}
						}

						.enname {
							margin-top: 10rpx;
							color: #a7a7a7;
							font-size: 26rpx;
							width: 400rpx;
							white-space: nowrap;
							overflow: hidden;
							text-overflow: ellipsis;
						}

						.price-step {
							margin-top: 30rpx;
							font-weight: 600;
							color: #173dbd;
							display: flex;
							justify-content: space-between;

							.uni-numbox__minus {
								width: 40rpx !important;
								height: 40rpx !important;
								border: 1px solid #c6cfef;
								border-radius: 50%;
								padding: 0 !important;
							}

							.uni-numbox__value {
								width: 60rpx;
							}

							.uni-numbox__plus {
								width: 40rpx !important;
								height: 40rpx !important;
								border: 1px solid #c6cfef;
								border-radius: 50%;
								background: #0c34ba !important;
								color: white !important;
								padding: 0 !important;

								span {
									color: white;
								}
							}
						}
					}
				}
			}
		}

		.footer-box {
			position: fixed;
			height: 150rpx;
			background: #ffffff;
			width: 100%;
			bottom: 0;

			.btn {
				height: 100rpx;
				background: #0c34ba;
				margin: 20rpx;
				border-radius: 50rpx;
				color: white;
				text-align: center;
				line-height: 100rpx;
			}
		}
	}
	
	.time-box{
		// background-color: pink;
		position: absolute;
		bottom: 0rpx;
		left: 0rpx;
		width: 95%;
		padding: 0 20rpx;
		
		.tiem{
			font-size: 23rpx;
			color: #a7a7a7;
		}
		
		.count-price{
			display: flex;
			justify-content: space-between;
		}
		
		.price{
			text{
				color: #0c34ba;
			}
		}
	}
</style>