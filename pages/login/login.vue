<template>
	<view class="login">
		<view class="login-first">
			<!-- 逛一逛 -->
			<view class="head">
				<view class="head-left">
					<!-- mode属性设置图片裁剪缩放方式，取值为widthfix -->
					<image class="logo" src="../../static/coffee.png" mode="widthFix"></image>
					<text>  Coffee</text>
				</view>
				<view class="head-right" @click="switch_menu">
					先逛一逛~
				</view>
			</view>
			<!-- 欢迎回来 -->
			<view class="first-welcome">
				<view class="welcome-title">
					欢迎回来
				</view>
				<view class="welcome-tips">
					Please login to your accounts
				</view>
			</view>
		</view>
		<view class="login-second">
			<uni-forms ref="login" :modelValue="login" :rules="rules">
				<uni-forms-item label="手机账号" name="phone">
					<uni-easyinput type="text" v-model="login.phone" placeholder="请输入11位手机号码" />
				</uni-forms-item>
				<uni-forms-item label="用户密码" name="password">
					<uni-easyinput class="input" v-model="login.password" type="password" placeholder="请输入密码" />
				</uni-forms-item>
			</uni-forms>
			<view class="seconde-forget-password">
				忘记密码了吗？
			</view>
			<button @click="clickLogin('login')" type="primary" class="seconde-login-btns btns-item">登录</button>
			<button type="primary" class="seconde-login-register btns-item" @click="open">注册</button>
			<uni-popup ref="popup" type="bottom" background-color="#fff">
				<view class="sun">
					<view class="font-f">
						注册
					</view>
					<view class="length">
						<uni-forms ref="register" :modelValue="register" :rules="rules">
							<uni-forms-item label="手机账号" name="phone">
								<uni-easyinput type="text" v-model="register.phone" placeholder="请输入11位手机号码" />
							</uni-forms-item>
							<uni-forms-item label="用户密码" name="password">
								<uni-easyinput class="input" v-model="register.password" type="password"
									placeholder="请输入密码" />
							</uni-forms-item>
							<uni-forms-item label="用户昵称" name='nickName'>
								<uni-easyinput class="input" v-model="register.nickName" type="text"
									placeholder="请输入昵称" />
							</uni-forms-item>
						</uni-forms>
						<button type="primary" class="register-but" @click="clickPhone('register')">注册</button>
					</view>
				</view>
			</uni-popup>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 表单数据
				login: {
					phone: "",
					password: ""
				},
				// register
				register: {
					phone: "",
					password: "",
					nickName: ""
				},
				rules: {
					// 对name字段进行必填验证
					phone: {
						rules: [{
							required: true,
							errorMessage: '手机号不能为空',
						}]
					},
					// 对email字段进行必填验证
					password: {
						rules: [{
							required: true,
							// format: 'email',
							errorMessage: '密码不能为空',
						}]
					},
					nickName: {
						rules: [{
							required: true,
							errorMessage: "昵称不能为空"
						}]
					}
				}
			}
		},
		methods: {
			clickLogin(ref) {
				this.$refs[ref].validate().then(res => {
					this.regFun(res.phone, /^1[3-9]\d{9}$/, "手机号码").then(() => {
						this.regFun(res.password, /\d{1,15}/, "用户密码").then(() => {
							uni.request({
								url: 'http://www.kangliuyong.com:10002/login', //仅为示例，并非真实接口地址。
								method: 'POST',
								data: {
									appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
									password: res.password,
									phone: res.phone
								},
								header: {
									'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
								},
								success: (res) => {
									console.log(res.data);
									if (res.data.code === 200) {
										// 存入token
										uni.setStorageSync('token', res.data.token);
										uni.showToast({
											title: res.data.msg,
											icon: "success",
											duration: 2000
										});
										uni.switchTab({
											url: '/pages/index/index',
										});

									} else {
										uni.showToast({
											title: res.data.msg,
											icon: "error",
											duration: 2000
										});
									}
								}
							});

						})
					})
				})
			},
			clickPhone(ref) {
				this.$refs[ref].validate().then(res => {
					console.log(res.data)
					//正则
					//1手机号码
					this.regFun(res.phone, /^1[3-9]\d{9}$/, "手机号码").then(() => {
						this.regFun(res.password, /\d{1,15}/, "用户密码").then(() => {
							this.regFun(res.nickName, /^[A-Za-z\d]{1,5}$/, '用户昵称').then(
								() => {
									uni.request({
										url: 'http://www.kangliuyong.com:10002/register',
										method: 'POST',
										data: {
											appkey: 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
											password: res.password,
											phone: res.phone,
											nickName: res.nickName
										},
										header: {
											'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8' //自定义请求头信息
										},
										success: (res) => {
											console.log(res.data);
											if (res.data.code === 100) {
												uni.showToast({
													title: res.data.msg,
													icon: "success",
													duration: 2000
												});
												for (var key in this.register) {
													this.register[key] = "";
												}
												this.$refs.popup.close()
											} else {
												uni.showToast({
													title: res.data.msg,
													icon: "error",
													duration: 2000
												});
											}
										}
									});

								})
						})
					})
				})
			},
			open() {
				this.$refs.popup.open('bottom')
			},
			switch_menu() {
				uni.switchTab({
					url: '/pages/index/index'
				});
			},
			// 表单验证
			regFun(str, reg, err) {
				// str:表示要验证的字符串（输入框内容）
				// reg:正则表达式
				// err:表示错误提示
				let issuccess = reg.test(str);
				console.log("issuccess=>", issuccess);
				let desc = "你输入" + err + "的格式不正确"

				return new Promise(reolve => {
					if (!issuccess) {
						uni.showToast({
							title: desc,
							duration: 2000
						});
					} else {
						reolve();
						// 页面跳转
						url: '/pages/index/index';
					}
				})
			},
			//开始验证

		}
	}
</script>

<style lang="scss">
	.login {
		// calc()动态计算公式,两边都要预留空格
		height: calc(100vh - 44px);
		background-color: #fff;

		.login-first {
			padding: 15rpx;

			.head {
				display: flex;
				justify-content: space-between;
				align-items: center;
				font-weight: bold;
				padding-bottom: 80px;

				.head-left {
					display: flex;
					align-items: center;

					.logo {
						width: 40px;
					}
				}

				.head-right {
					color: #0c34ba;
					font-family: 黑体;
				}
			}

			.first-welcome {
				.welcome-title {
					font-weight: bold;
					font-family: 黑体;
					font-size: 30px;
					color: #646566;
				}

				.welcome-tips {
					padding: 20px 0 30px;
					font-weight: lighter;
					color: #8f8b8b;
				}
			}
		}

		.login-second {
			padding: 15px;

			::v-deep .uni-forms-item__label {
				width: 80px !important;
			}

			.seconde-forget-password {
				text-align: right;
				padding-bottom: 25px;
				color: #8f8b8b;
			}

			.btns-item {
				width: 95%;
				border-radius: 18px;
				font-size: 15px;
			}

			.seconde-login-btns {
				background-color: #0c34ba;
			}

			.seconde-login-register {
				border: 1px solid #ccc;
				background-color: #fff;
				color: #600000;
				margin-top: 30px;
			}
		}
	}

	.sun {
		width: 95%;
		height: 280px;
		padding: 15px;
		background-color: #fff;

		.font-f {
			height: 20px;
			margin-top: 20px;
			margin-bottom: 20px;
			margin-left: -10px;
			font-size: 20px;
			font-weight: bold;
			color: #646500;
		}

		.register-but {
			width: 95%;
			// margin-left: 10px;
			border-radius: 23px;
			border: 1px solid #ccc;
			background-color: #0c34ba;
		}
	}
</style>