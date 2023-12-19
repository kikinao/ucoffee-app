<template>
	<view>
		<view class="head">
			<view class="back">
				<uni-icons type="back" size="30" color="#0c34ba"></uni-icons>
				<view class="back-text spc" @click="back">
					返回
				</view>
			</view>
			<view class="cart">
				<text>我的收藏</text>
			</view>
		</view>
		<view class="mid">

		</view>
		<view class="info">
			<view class="col" v-for="(item,index) in collections">
				<image :src="item.smallImg" mode="widthFix" class="img"></image>
				<view class="text-a">
					<view class="text-top">
						<span>{{item.name}}</span><br>
						<span class="en">{{item.enname}}</span>
					</view>
					<view class="text-bottom">
						<view class="price">
							￥{{item.price}}
						</view>
						<view class="del" @click="delete_(item.pid)">
							<uni-icons type="trash-filled" size="20"></uni-icons>
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				collections: []
			};
		},
		onShow() {
			this.getcollection()
		},
		methods: {
			getcollection() {
				uni.request({
					// 查询用户所有收藏商品接口
					//  请求地址：http://www.kangliuyong.com:10002/findAllLike
					//  请求类型： GET
					//  请求参数： {
					//    appkey: 你的appkey,
					//    tokenString: token字符串
					//  }
					url: "http://www.kangliuyong.com:10002/findAllLike",
					method: "GET",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString: uni.getStorageSync('token')
					},
					success: (res) => {
						if (res.data.code === 2000) {
							this.collections = res.data.result
						}
					}
				})
			},
			back() {
				uni.switchTab({
					url: "/pages/my/my"
				})
			},
			delete_(pid) {
				uni.request({
					url: "http://www.kangliuyong.com:10002/notlike",
					method: "post",
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						pid: pid,
						tokenString: uni.getStorageSync('token')
					},
					success: (res) => {
						if (res.data.code === 900) {
							uni.showToast({
								title: res.data.msg,
								duration: 2000
							})
							this.getcollection()
						}
					}
				})
			},
		}
	}
</script>

<style lang="scss">
	.head {
		display: flex;
		justify-content: space-between;
		padding: 10px 0;
		background-color: #fefefe;

		.back {
			display: flex;
			color: #0c34ba;
			// margin-left: 10px;
			justify-content: space-between;
		}

		.cart {
			flex: 1;
			font-size: 18px;
			margin-left: 24%;
			margin-top: 0.2%;

		}
	}

	.mid {
		width: 100%;
		height: 100px;
		background-color: #0c34ba;
		position: absolute;
		z-index: 1;
	}

	.info {
		display: flex;
		// flex-direction: column;
		justify-content: space-between;
		flex-wrap: wrap;
		width: 95%;
		position: absolute;
		z-index: 2;
		margin-top: 20%;
		border-radius: 10px;
		background-color: #fefefe;
		margin-left: 2.5%;

		.col {
			// flex: 1;
			width: 42%;
			margin: 10px;
			display: flex;
			flex-direction: column;

			.img {
				width: 100%;
				height: 20px;
			}

			.text-a {
				width: 100%;

				.text-top {
					font-size: 16px;
					color: #6f6f7c;
					overflow: hidden;
					white-space: nowrap;
					text-overflow: ellipsis;

					.en {
						width: 100%;
					}
				}

				.text-bottom {
					width: 100%;
					display: flex;
					justify-content: space-between;

					.price {
						color: #0c34ba;
						font-weight: bold;
						font-size: 18px;
					}

				}
			}
		}
	}

	.spc {
		margin-top: 3%;
	}
</style>