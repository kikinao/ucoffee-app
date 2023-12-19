<template>
	<view class="total">
		<view class="head">
			<image :src="info.largeImg" mode="widthFix" class="img-s"></image>
		</view>
		<view class="mid">
			<view class="text">{{info.name}}<br><span class="text1">{{info.enname}}</span></view>
			<view class="price">
				￥{{info.price}}
			</view>
			<view class="check" v-if="temperature === true">
				<span class="title">温度</span>
				<view class="list-box">
					<view v-for="(item,index) in list" :key="index" @click="choice(index)"
						:class="[item.selected?'selde':'noselde']" class="but">
						{{item.selected?item.title:item.title}}
					</view>
				</view>
			</view>
			<view class="check" v-if="butter === true">
				<span class="title">奶油</span>
				<view class="list-box">
					<view v-for="(item,index) in cream" :key="index" @click="hhh(index)"
						:class="[item.selected?'selde':'noselde']" class="but">
						{{item.selected?item.title:item.title}}
					</view>
				</view>
			</view>
			<view class="check" v-if="sugar_ === true">
				<span class="title">糖</span>
				<view class="list-box">
					<view v-for="(item,index) in suagerlist" :key="index" @click="select(index)"
						:class="[item.selected?'selde':'noselde']" class="but">
						{{item.selected?item.title:item.title}}
					</view>
				</view>
			</view>
			<hr class="today">
			<view class="check">
				<span class="title">选择数量</span>
				<view class="number">
					<uni-number-box v-model="numbervalue" background="#0c34ba" color="#fff" />
				</view>
			</view>
			<hr class="today">
			<view class="mid-bottom">
				<view class="b-title">
					商品详情
				</view>
				<view class="b-item" v-for="(item,index) in split(info.desc)">
					<view class="text-b">
						{{index+1}}.{{item}}
					</view>
				</view>
			</view>
			<view class="bottom">
				<uni-section>
					<uni-goods-nav :fill="true" :options="options" :button-group="customButtonGroup" @click="onClick"
						@buttonClick="buttonClick" />
				</uni-section>
			</view>
		</view>

	</view>
</template>

<script>
	export default {
		onLoad() {
			let a = uni.getStorageSync('infomation')
			this.info = a
			// console.log(this.info);
			this.getdetail()
			this.get_cart()
			this.selectcream = this.cream[0]
			this.selectList = this.list[0]
			this.selectsugarList = this.suagerlist[0]
		},
		data() {
			return {
				temperature: true,
				butter: true,
				sugar_: true,
				info: {},
				//定义前端选中的值
				selectList: "",
				numbervalue: 1,
				options: [{
						icon: 'cart',
						text: '购物袋',
						info: 0,
						infoBackgroundColor: '#007aff',
						infoColor: "#f5f5f5"
					},
					{
						icon: 'heart',
						text: '未收藏',
						info: 0,
						infoBackgroundColor: '#007aff',
						infoColor: "#f5f5f5"
					}
				],
				buttonGroup: [{
						text: '加入购物车',
						backgroundColor: 'linear-gradient(90deg, #FFCD1E, #FF8A18)',
						color: '#fff'
					},
					{
						text: '立即购买',
						backgroundColor: 'linear-gradient(90deg, #FE6035, #EF1224)',
						color: '#fff'
					}
				],
				customButtonGroup: [{
						text: '加入购物袋',
						backgroundColor: 'linear-gradient(90deg,#60F3FF, #60F3FF )',
						color: '#fff'
					},
					{
						text: '立即购买',
						backgroundColor: 'linear-gradient(90deg, #1E83FF, #0c34ba)',
						color: '#fff'
					}
				],
				//给标签赋值
				list: [],
				selectcream: "",
				cream: [{
					selected: false,
					title: '默认奶油'
				}, {
					selected: false,
					title: '无奶油'
				}],
				selectsugarList: "",
				//给标签赋值
				suagerlist: [],
			};
		},
		methods: {
			choice(index) {
				if (this.list.length === 2) {
					let a = (index == 1 ? 0 : 1)
					this.list[index].selected = true
					this.list[a].selected = false
					this.selectList = this.list[index]
				} else {
					this.list[index].selected = true
					this.selectList = this.list[index]
				}
				// console.log(this.selectList)
			},
			get_cart() {
				// 请求地址：http://www.kangliuyong.com:10002/shopcartCount
				//   请求类型： GET
				//   请求参数： {
				//     appkey: 你的appkey,
				//     tokenString: token字符串
				//   }
				uni.request({
					url: "http://www.kangliuyong.com:10002/shopcartCount",
					method: "get",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString: uni.getStorageSync('token')
					},
					success: (res) => {
						if (res.data.code === 4000) {
							this.options[0].info = res.data.result
						}
					}
				})
				// 根据商品pid查询收藏商品接口
				//   请求地址：http://www.kangliuyong.com:10002/findlike
				//   请求类型： GET
				//   请求参数： {
				//     appkey: 你的appkey,
				//     pid: 商品pid,
				//     tokenString: token字符串
				//   }
				uni.request({
					url: "http://www.kangliuyong.com:10002/findlike",
					method: "get",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						pid: this.info.pid,
						tokenString: uni.getStorageSync('token')
					},
					success: (res) => {
						if (res.data.code === 1000) {
							if (res.data.result.length !== 0) {
								this.options[1].text = "已收藏"
								this.options[1].icon = "heart-filled"
							}
						}
					}
				})
			},
			hhh(index) {
				let a = (index == 1 ? 0 : 1)
				this.cream[index].selected = true
				this.cream[a].selected = false
				this.selectcream = this.cream[index]
				// console.log(this.selectcream)
			},
			split(text) {
				return text.split("\n").filter((sentence) => sentence.trim() !== "")
			},
			split_(text) {
				return text.split("/").filter((sentence) => sentence.trim() !== "")
			},
			select(index) {
				for (var i = 0; i < this.suagerlist.length; i++) {
					if (i != index) {
						this.suagerlist[i].selected = false
					}
				}
				this.suagerlist[index].selected = true
				this.selectsugarList = this.suagerlist[index]
				console.log(this.selectsugarList);
			},
			onClick(e) {
				let temp = e.content.text
				// console.log(temp);
				if (temp === "购物袋") {
					uni.switchTab({
						url: "/pages/cart/cart",
					})
				} else {
					if (temp == "未收藏") {
						uni.request({
							url: "http://www.kangliuyong.com:10002/like",
							method: "POST",
							header: {
								'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
							},
							data: {
								appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
								pid: this.info.pid,
								tokenString: uni.getStorageSync('token')
							},
							success: (res) => {
								if (res.data.code === 800) {
									uni.showToast({
										title: res.data.msg,
										duration: 2000
									})
								} else {
									uni.showToast({
										title: "收藏失败，请登录",
										duration: 2000
									});
									uni.navigateTo({
										url: '../login/login'
									});
								}
							}
						})
						this.options[1].text = "已收藏"
						this.options[1].icon = "heart-filled"
					} else {
						uni.request({
							url: "http://www.kangliuyong.com:10002/notlike",
							method: "POST",
							header: {
								'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
							},
							data: {
								appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
								pid: this.info.pid,
								tokenString: uni.getStorageSync('token')
							},
							success: (res) => {
								// console.log(res.data);
								if (res.data.code === 900) {
									uni.showToast({
										title: res.data.msg,
										duration: 3000
									})
								}
							}
						})
						this.options[1].text = "未收藏"
						this.options[1].icon = "heart"
					}
				}

			},
			getdetail() {
				uni.request({
					url: "http://www.kangliuyong.com:10002/productDetail",
					method: "get",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						pid: this.info.pid
					},
					success: (res) => {
						var ans = res.data.result[0]
						if (ans.cream === "") {
							this.butter = false
						}
						if (ans.sugar === "") {
							this.sugar_ = false
						}
						if (ans.tem === "") {
							this.temperature = false
						}
						if (ans.tem !== "") {
							var a = this.split_(ans.tem)
							// console.log(a.length);

							for (var i = 0; i < a.length; i++) {
								var obj = {
									selected: false,
									title: a[i]
								}
								this.list.push(obj)
							}
						}
						if (ans.sugar !== "") {
							var a = this.split_(ans.sugar)
							// console.log(a.length);
							for (var i = 0; i < a.length; i++) {
								var obj = {
									selected: false,
									title: a[i]
								}
								this.suagerlist.push(obj)
							}
						}
					}
				})
			},
			buttonClick(e) {
				var boj = {
					tempera: this.temperature ? this.selectList.title : "",
					sugar: this.sugar_ ? this.selectsugarList.title : "",
					cream: this.butter ? this.selectcream.title : "",
				}
				if (e.index === 0) {
					//拼接规格
					var temp = [0, 1, 2]
					//温度输入数组
					temp[0] = boj.tempera
					//糖分
					temp[1] = boj.sugar
					//奶油
					temp[2] = boj.cream
					//目标字符串
					let ans = ""
					for (var i = 0; i < temp.length; i++) {
						if (temp[i] !== "") {
							ans += temp[i] + "/"
						}
					}
					ans = ans.substr(0, ans.length - 1);
					var data = {
						pid: this.info.pid,
						count: this.numbervalue,
						rule: ans,
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString: uni.getStorageSync('token')
					}
					// console.log(data);
					uni.request({
						url: "http://www.kangliuyong.com:10002/addShopcart",
						method: "POST",
						header: {
							'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
						},
						data,
						success: (res) => {
							if (res.data.code === 3000) {
								uni.showToast({
									title: res.data.msg,
									duration: 2000
								});
								this.options[0].info += this.numbervalue
							} else {
								uni.showModal({
									title: '提示',
									content: '看起来你还没有登录，跳转登录？',
									success: function(res) {
										if (res.confirm) {
											uni.navigateTo({
												url: '../login/login'
											});
										} else if (res.cancel) {

										}
									}
								});
							}
						}
					})

				} else {
					console.log("我是立即购买");
				}

			},
		}
	}
</script>

<style lang="scss">
	.today {
		width: 100%;
		color: #000;
		height: 0.2px;
		// border-bottom: 1px solid black;
	}

	.total {
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
		padding: 0 10px;
		background-color: #fff;

		.head {
			width: 95%;
			height: 44%;
			position: absolute;
			z-index: 1;

			.img-s {
				width: 100%;
				height: 370px;
			}
		}

		.mid {
			width: 85%;
			display: flex;
			height: 80%;
			justify-content: space-between;
			flex-wrap: wrap;
			padding: 10px;
			margin-left: 2.5%;
			margin-top: 65%;
			position: absolute;
			border-radius: 10px 10px 0 0;
			z-index: 2;
			background-color: #ffffff;

			.bottom {
				width: 100%;
				background-color: #f5f5f5;
				position: sticky;
				z-index: 999;
				bottom: 0;
			}

			.mid-bottom {
				width: 100%;
				// height: 80%;

				.b-title {
					width: 100%;
					height: 10%;
					padding: 10px 0;
					font-size: 18px;
				}

				.b-item {
					width: 100%;
					height: 18%;
					padding: 10px 0;
				}
			}

			.text {
				width: 60%;
				display: flex;
				font-size: 16px;
				color: #646464;
				flex-direction: column;
				font-weight: bold;


			}

			.price {
				width: 20%;
				margin-top: 2%;
				font-size: 18px;
				font-weight: bold;
				text-align: right;
				color: #0c34ba;
			}

			.count {
				// margin-bottom: 10px;
				height: 40px;
				border-bottom: 1px solid black;
			}

			.check {
				display: flex;
				justify-content: space-between;
				margin-top: 10px;
				// height: 100px;
				padding: 10px;
				width: 100%;




				.list-box {
					width: 80%;
					height: 30px;
					display: flex;
					margin-left: 0px;

					.but {
						width: 30%;
						height: 100%;
						margin-left: 10px;
						display: flex;
						align-items: center;
						justify-content: center;
						border-radius: 20px;
					}

					.selde {
						color: white;
						background-color: #0c34ba;
					}

					.noselde {
						color: #5f6366;
						background-color: #e8e8e8;
					}
				}
			}

			.bot {
				height: 50px;
				border-bottom: 1px solid black;
			}

		}


	}



	.text1 {
		width: 60%;
		font-weight: normal;
		font-size: 12px;
		overflow: hidden;
		white-space: nowrap;
		text-overflow: ellipsis;
		color: #5f6366;
	}

	.title {
		width: 25%;
		// color: #0c34ba;
		margin-top: 1%;
	}

	.text-b {
		width: 100%;
	}

	::v-deep .uni-numbox__value {
		background-color: #fff !important;
		color: #000 !important;
	}

	::v-deep .uni-numbox-btns {
		background-color: #0c34ba !important;
		border-radius: 50% !important;
	}

	::v-deep .uni-numbox__minus {
		background-color: #fff !important;
		border: 1px solid black !important;
		color: #000 !important;

	}

	::v-deep .uni-numbox--text {
		color: white !important;
	}

	::v-deep .uni-numbox--text {
		color: #000 !important;
	}
</style>