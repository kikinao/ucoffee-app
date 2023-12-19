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
				<text>个人资料</text>
			</view>
		</view>
		<view class="mid">

		</view>
		<view class="info">
			<uni-table class="tab">
				<uni-tr @click="person">
					<uni-td class="text">
						<view class="left">
							头像
						</view>
						<view class="right">
							<image :src="users.userImg" mode="widthFix" class="img-s"></image>
						</view>
					</uni-td>
				</uni-tr>
				<uni-tr>
					<uni-td class="text">
						<view class="left">
							用户id
						</view>
						<view class="right">
							{{users.userId}}
						</view>
					</uni-td>
				</uni-tr>
				<uni-tr>
					<uni-td class="text" @click="changenickName">
						<view class="left">
							昵称
						</view>
						<view class="right">
							{{users.nickName}}
						</view>
					</uni-td>
				</uni-tr>
				<uni-tr>
					<uni-td class="text" @click="changeDesc">
						<view class="left">
							个性签名
						</view>
						<view class="right" v-if="users.desc !== ''">
							{{users.desc}}
						</view>
						<view class="right" v-else>
							这家伙很懒，什么都没留下
						</view>
					</uni-td>
				</uni-tr>
			</uni-table>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				users: {}
			};
		},
		onShow() {
			this.getuser()
		},
		methods: {
			getuser() {
				uni.request({
					// 个人资料接口
					//   请求地址：http://www.kangliuyong.com:10002/findAccountInfo
					//   请求类型： GET
					//   请求参数： {
					//     appkey: 你的appkey,
					//     tokenString: token字符串
					//   }
					url: 'http://www.kangliuyong.com:10002/findAccountInfo', //仅为示例，并非真实接口地址。
					data: {
						appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
						tokenString: uni.getStorageSync('token')
					},
					success: (res) => {
						if (res.data.code === 'B001') {
							this.users = res.data.result[0]
						} else {
							var obj = {
								"nickName": "访客用户",
								"userImg": "http://www.kangliuyong.com:10002/assets/default.png",
								"userBg": "http://www.kangliuyong.com:10002/assets/default_bg.jpg",
								"desc": "您看起来还未登录，请登录...",
								"vip": 0
							}
							this.users = obj
						}
					}
				});
			},
			changeDesc() {
				// 修改简介接口
				//  请求地址：http://www.kangliuyong.com:10002/updateDesc
				//  请求类型： POST
				//  请求参数： {
				//    appkey: 你的appkey,
				//    tokenString: token字符串,
				//    desc: 简介
				//  }
				uni.showModal({
					title: "修改简介",
					placeholderText: "请输入简介",
					editable: true,
					success: function(res) {
						if (res.confirm) {
							uni.request({
								url: 'http://www.kangliuyong.com:10002/updateDesc',
								method: 'POST',
								data: {
									appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
									desc: res.content,
									tokenString: uni.getStorageSync('token')
								},
								header: {
									'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8' //自定义请求头信息
								},
								success: (res) => {
									if (res.data.code === 'B001') {
										this.users.desc = res.content
										uni.showToast({
											title: "修改成功",
											duration: 2000
										})
									}

								}
							})
						}
					}.bind(this)
				})
			},
			changenickName() {
				// 修改昵称接口
				//  请求地址：http://www.kangliuyong.com:10002/updateNickName
				//  请求类型： POST
				//  请求参数： {
				//    appkey: 你的appkey,
				//    tokenString: token字符串,
				//    nickName: 你的昵称
				//  }
				uni.showModal({
					title: "修改昵称",
					placeholderText: "请输入昵称",
					editable: true,
					success: function(res) {
						if (res.confirm) {
							var name = {
								url: "http://www.kangliuyong.com:10002/updateNickName",
								data: {
									tokenString: uni.getStorageSync('token'),
									appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
									nickName: res.content
								},
							}
							this.postmt(name.url, name.data)
							//在起始页面跳转到test.vue页面并传递参数
							//在起始页面跳转到test.vue页面并传递参数
							this.users.nickName = res.content
							uni.showToast({
								title: "修改昵称成功",
								duration: 2000
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}.bind(this)
				})
			},
			postmt(url, data) {
				uni.request({
					url,
					method: "post",
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data,
					success: (res) => {
						console.log(res.data);
					}
				})
			},
			back() {
				uni.switchTab({
					url: "/pages/my/my"
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

	.text {
		display: flex;
		justify-content: space-between;
		width: 100%;
	}

	.left {
		line-height: 50px;
		text-align: center;
		color: #6f6f7c;
	}

	.right {
		line-height: 50px;
		text-align: center;
		color: #6f6f7c;
	}

	.img-s {
		border-radius: 50%;
		margin-top: 5px;
		width: 50px;
		height: 50px;
	}

	.info {
		width: 95%;
		position: absolute;
		z-index: 2;
		margin-top: 20%;
		margin-left: 2.5%;
	}

	.spc {
		margin-top: 3%;
	}
</style>