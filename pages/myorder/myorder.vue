<template>
	<view class="order">
		<view class="gotoback" @click="gotoback">
			<uni-nav-bar fixed left-icon="left" left-text="返回" title="订单管理"></uni-nav-bar>
		</view>

		<view class="bg"></view>


		<!-- 进行中 全部 已完成 -->

		<view class="choose">
			<view class="item" :class="status==0?'active':''" @click="changeStatus(0)">全部</view>
			<view class="item" :class="status==1?'active':''" @click="changeStatus(1)">进行中</view>
			<view class="item" :class="status==2?'active':''" @click="changeStatus(2)">已完成</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				//其中 status 值为0，1，2 ==》 0：全部 1：进行中 2：已完成
				status: 0,
				token: ""
			}
		},


		onShow() {
			//获取token
			this.token = uni.getStorageSync("token")
			//调用一下方法
			this.getOrder()


		},


		methods: {
			gotoback() {
				uni.switchTab({
					url: "/pages/my/my"
				})
			},
			changeStatus(index) {
				this.status = index;
				this.getOrder()
			},
			//获取我们对应的订单
			getOrder() {

				if (!this.token) {
					uni.navigateTo({
						url: "/pages/login/login"
					})
					return
				}
				uni.request({


					url: "http://www.kangliuyong.com:10002/findOrder",
					// 请求类似如果是get可以不用写的
					// 参数
					//method: "POST",
					data: {
						appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
						tokenString: this.token,
						status: this.status


					},

					header: {
						'content-type': 'application/x-www-form-urlencoded;charset=utf-8' // 默认值
					},

					// 成功时候执行的
					success: (res) => {
						console.log('res', res)
						//this.bannerData = res.data.result
					}
				})

			}

		}
	}
</script>

<style lang="scss">
	.order {
		.bg {
			height: 200rpx;
			background: #0c34ba;
		}

		.choose {
			margin: 30rpx;
			margin-top: -50rpx;
			height: 100rpx;
			background: white;
			display: flex;
			border-radius: 30rpx 30rpx 0 0;
			justify-content: space-between;
			align-items: center;
			padding: 0 40rpx;
			line-height: 100rpx;

			.item {
				width: 100rpx;
				height: 100%;
				text-align: center;
				color: #726d74;
				//border-bottom: 4rpx solid #0c34ba;

			}

			.active {
				border-bottom: 4rpx solid #0c34ba;
				color: #0c34ba;
			}
		}

	}
</style>