<template>
	<view class="total">
		<view class="head">
			<view class="back-pic">
				<image :src="users.userBg" mode="widthFix" class="pic"></image>
			</view>
		</view>
		<view class="info">
			<view class="info-head">
				<image :src="users.userImg" mode="widthFix" class="pic-s" @click="login"></image>
				<view class="text-u">
					<view class="nickname">
						{{users.nickName}}
					</view>
					<view class="desc" @click="changenickName">
						<span v-if="users.desc === ''">这个家伙很懒，什么都没留下!</span>
						<span v-else>{{users.desc}}</span>
					</view>
				</view>
			</view>
			<view class="info-menu">
				<view class="data">
					<uni-table class="tab">
						<uni-tr class="text" @click="person">
							<uni-td>个人资料<span class="end">></span></uni-td>
						</uni-tr>
						<uni-tr class="text" @click="myorder">
							<uni-td>我的订单<span class="end">></span></uni-td>
						</uni-tr>
						<uni-tr class="text" @click="collection">
							<uni-td>我的收藏<span class="end">></span></uni-td>
						</uni-tr>
						<uni-tr class="text" @click="address">
							<uni-td>地址管理<span class="end">></span></uni-td>
						</uni-tr>
					</uni-table>

				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		onShow() {
			this.getuser()
		},
		data() {
			return {
				users: {},
				islogin: false,
			}
		},
		methods: {
			myorder() {
				uni.navigateTo({
					url: "../myorder/myorder"
				})
			},
			address() {
				uni.navigateTo({
					url: "../address/address"
				})
			},
			person() {
				uni.navigateTo({
					url: '../person/person'
				});
			},
			collection() {
				uni.navigateTo({
					url: '../collection/collection'
				})
			},
			getuser() {
				uni.request({
					url: 'http://www.kangliuyong.com:10002/findMy', //仅为示例，并非真实接口地址。
					data: {
						appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
						tokenString: uni.getStorageSync('token')
					},
					success: (res) => {
						if (res.data.code === 'A001') {
							this.users = res.data.result[0]
							this.islogin = true
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
			logout() {
				uni.request({
					url: "http://www.kangliuyong.com:10002/logout",
					method: "post",
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString: uni.getStorageSync('token')
					},
					success: (res) => {
						console.log(res.data);
					}
				})
			},
			changedesc() {

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
							uni.switchTab({
								url: '/pages/index/index'
							});



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
			login() {
				if (this.islogin) {
					uni.showModal({
						title: '提示',
						content: '想要注销登陆？',
						success: function(res) {
							if (res.confirm) {
								this.logout()
								this.islogin = false
								uni.navigateTo({
									url: '../login/login'
								});
							} else if (res.cancel) {
								// reject()
							}
						}.bind(this)
					});
				} else {
					uni.navigateTo({
						url: '../login/login'
					});
				}
			},
		}
	}
</script>


<style lang="scss">
	.head {
		width: 100%;
		// padding: 0 10px;
		position: absolute;
		z-index: 1;

		.back-pic {
			width: 100%;

			.pic {
				width: 100%;
				height: 100%;
			}
		}
	}

	.info {
		width: 90%;
		position: absolute;
		margin-top: 40%;
		margin-left: 2.5%;
		padding: 10px;
		border-radius: 15px;
		z-index: 2;
		background-color: rgba(255, 255, 255, 0.8);

		.info-head {
			width: 100%;
			height: 10%;
			display: flex;
			justify-content: space-between;

			.pic-s {
				width: 25%;
				border-radius: 50%;
			}

			.text-u {
				display: flex;
				flex: 1;
				flex-direction: column;
				margin-left: 10px;

				.nickname {
					height: 40%;
					margin-top: 1%;
					color: #0c34ba;
					font-weight: bold;
					font-size: 20px;
				}

				.desc {
					color: #6f6f7c;
				}
			}
		}

		.info-menu {
			width: 100%;
			margin-top: 100px;

			.data {
				width: 100%;

				.tab {
					width: 100%;
				}
			}
		}
	}

	::v-deep .uni-table-td {
		height: 50px !important;
		display: flex;
		justify-content: space-between;
	}

	.end {
		// width: 10%;
		height: 20px;
	}
</style>