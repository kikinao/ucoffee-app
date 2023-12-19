<template>
	<view class="">
		<view class="head">
			<view class="back">
				<uni-icons type="back" size="30" color="#0c34ba"></uni-icons>
				<view class="back-text spc">
					返回
				</view>
			</view>
			<view class="cart">
				<text>购物袋</text>
			</view>
			<view class="edit" @click="show">
				<text>编辑</text>
			</view>
		</view>
		<view class="head-img">
			<!-- <image src="" class="img-s"></image> -->
		</view>
		<view class="mid">
			<view class="info-m" v-if="info.length !== 0 && isShow === true">
				<view class="objs" v-for="(item, index) in info">
					<view class="info-img">
						<checkbox-group @change="select(index)">
							<checkbox :value="选中" :checked="item.ischoice" />
						</checkbox-group>
						<image :src="item.small_img" mode="widthFix" class="img"></image>
					</view>
					<view class="text">
						<view class="top">
							<view class="larg">
								{{item.name}}
								<span class="mid-t">{{item.rule}}</span>
							</view>
							<view class="outly">{{item.enname}}</view>
						</view>
						<view class="bottom">
							<view class="price">
								￥{{item.price * item.count}}
							</view>
							<view class="myCount">
								<uni-number-box v-model="item.count" color="#fff" />
							</view>
						</view>
					</view>
				</view>
			</view>
			<view class="info-n" v-else>
				<view class="gologin" v-if="!isShow" @click="gologin">
					<span class="title">空空如也</span><br />
					您看起来还没登录，点我去登录...
				</view>
				<view class="gomenu" v-else @click="gomenu">
					<span class="title">空空如也</span><br />
					购物车是空的，点我去菜单看看吧...
				</view>
			</view>
		</view>
		<view class="bottom-b" v-if="info.length !== 0 && isShow === true">
			<view class="check-b">
				<checkbox-group @change="allselect">
					<checkbox :value="全选" :checked="sumselect" />
				</checkbox-group>
				<view class="text-b">
					全选
				</view>
			</view>
			<view class="other" v-if="!isdelete">
				<view class="sum">
					合计:<span class="m">￥</span><span class="int">{{inte}}</span><span class="flo">.{{float_n}}</span>
				</view>
				<button class="sub" @click="gotopay">提交订单</button>
			</view>
			<!-- 这里写删除 -->
			<view class="other" v-else>
				<button class="del">删除</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		onShow() {
			this.getcart();
			this.map();
			this.float();
		},

		data() {
			return {
				sid: [],
				info: [],
				isShow: false,
				sums: 0,
				inte: "0",
				float_n: "0",
				isdelete: false,
				sumselect: false,
				carData: [],
			};
		},
		methods: {
			gotopay() {
				this.sids = [];
				this.carData.map(item => {
					if (item.choose) {
						this.sids.push(item.sid);
					}
				})

				if (this.sids.length == 0) {
					uni.showToast({
						title: "请勾选商品",
						icon: "error"
					});
					return;
				}

				uni.navigateTo({
					url: "/pages/pay/pay?sids=" + JSON.stringify(this.sids)
				});
			},
			select(index) {
				if (this.info[index].ischoice) {
					this.info[index].ischoice = false;
					this.sums -= this.info[index].count * this.info[index].price;
				} else {
					this.info[index].ischoice = true;
					this.sums += this.info[index].count * this.info[index].price;
				}
				var num = this.sums.toFixed(2);
				this.inte = this.split(num)[0];
				this.float_n = this.split(num)[1];

				var selectedItems = this.info.filter(item => item.ischoice);
				if (selectedItems.length === this.info.length) {
					this.sumselect = true;
				} else {
					this.sumselect = false;
				}

				// 更新this.carData
				this.carData = this.info.map(item => {
					item.choose = item.ischoice;
					return item;
				});
			},

			allselect(e) {
				if (this.sumselect) {
					this.sumselect = false;
					for (var i = 0; i < this.info.length; i++) {
						this.info[i].ischoice = false;
						this.sums -= this.info[i].count * this.info[i].price;
					}
				} else {
					this.sumselect = true;
					for (var i = 0; i < this.info.length; i++) {
						this.info[i].ischoice = true;
						this.sums += this.info[i].count * this.info[i].price;
					}
				}
				var num = this.sums.toFixed(2);
				this.inte = this.split(num)[0];
				this.float_n = this.split(num)[1];

				var selectedItems = this.info.filter(item => item.ischoice);
				if (selectedItems.length === this.info.length) {
					this.sumselect = true;
				} else {
					this.sumselect = false;
				}

				// 更新this.carData
				this.carData = this.info.map(item => {
					item.choose = item.ischoice;
					return item;
				});
			},


			float() {
				var num = this.sums.toFixed(2);
				this.inte = this.split(num)[0];
				this.float_n = this.split(num)[1];
			},
			split(text) {
				return text.split(".").filter(sentence => sentence.trim() !== "");
			},
			map() {
				this.info = this.info.map(item => {
					item.ischoice = false;
					return item;
				});
			},
			gologin() {
				uni.navigateTo({
					url: "../login/login"
				});
			},
			gomenu() {
				uni.switchTab({
					url: "/pages/menu1/menu1"
				});
			},
			getcart() {
				uni.request({
					url: "http://www.kangliuyong.com:10002/findAllShopcart",
					method: "GET",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString: uni.getStorageSync("token")
					},
					success: res => {
						if (res.data.code === 5000) {
							this.info = res.data.result;
							this.carData = res.data.result;
							this.isShow = true;
						}
					}
				});
			},
			show() {
				this.isdelete = !this.isdelete;
			},
			calculateTotal() {
				this.sums = 0;
				for (var i = 0; i < this.info.length; i++) {
					if (this.info[i].ischoice) {
						this.sums += this.info[i].count * this.info[i].price;
					}
				}
				this.float();
			}
		},
		watch: {
			'info': {
				deep: true,
				handler: function() {
					this.calculateTotal();
				}
			}
		},
	};
</script>




<style lang="scss">
	.del {
		width: 50%;
		height: 90%;
		margin-left: 40%;
		background-color: red;
		color: #fefefe;
		border-radius: 20px;
	}

	.head {
		display: flex;
		justify-content: space-between;
		padding: 10px;
		background-color: #fefefe;

		.back {
			display: flex;
			color: #0c34ba;
			justify-content: space-between;
		}

		.cart {
			flex: 1;
			font-size: 18px;
			margin-left: 28%;
			margin-top: 0.2%;
		}

		.edit {
			margin-top: 0.8%;
			margin-right: 2%;
		}
	}

	.head-img {
		width: 100%;
		height: 150px;

		background-image: url(../../static/cart-t.jpg);
		background-size: cover;
		background-repeat: no-repeat;
		background-position: center center;
	}

	.mid {
		width: 95%;
		padding: 0 10px;

		.info-m {
			width: 100%;

			.objs {
				width: 100%;
				display: flex;
				justify-content: space-between;
				margin-top: 10px;
				border-radius: 10px;
				background-color: #fefefe;
				padding: 15rpx;

				.info-img {
					display: flex;
					justify-content: space-between;
					width: 40%;

					::v-deep .uni-checkbox-input {
						// width: 20%;
						margin-top: 180% !important;
						border: 1px solid #6f6f7c !important;
						border-radius: 50% !important;
					}

					.img {
						width: 80%;
						height: 40px;
						border-radius: 10rpx;
					}
				}

				.text {
					display: flex;
					flex-direction: column;
					justify-content: space-between;
					flex: 1;
					margin-left: 10px;

					.top {
						width: 100%;
						height: 50%;
						// padding: 10px 0;

						.larg {
							font-weight: bold;
							color: #6f6f7c;
							font-size: 20px;

							.mid-t {
								font-size: 16px;
								font-weight: normal;
								margin-left: 10px;
							}
						}

						.outly {
							color: #6f6f7c;
							width: 100%;
							margin-top: 10px;
						}
					}

					.bottom {
						width: 100%;
						display: flex;
						justify-content: space-between;
						margin-bottom: 10px;

						.price {
							width: 50%;
							font-size: 18px;
							font-weight: bold;
							color: #0c34ba;
						}
					}
				}
			}
		}

		.info-n {
			width: 100%;
			margin-top: 70%;
			margin-left: 20%;

			.gologin {
				width: 70%;
				height: 30px;
				font-size: 16px;
				color: #6f6f7c;

				.title {
					width: 50%;
					margin-left: 30%;
					font-weight: bold;
				}
			}

			.gomenu {
				width: 70%;
				height: 30px;
				font-size: 16px;
				color: #6f6f7c;

				.title {
					width: 50%;
					margin-left: 30%;
					font-weight: bold;
				}
			}
		}
	}

	.text-more {
		width: 100%;
		font-size: 20px;
		color: #6f6f7c;
		text-align: center;
		margin-top: 10px;
	}

	.bottom-b {
		width: 100%;
		height: 50px;
		position: sticky;
		z-index: 999;
		bottom: 50px;
		margin-top: 100%;
		// margin-bottom: 0px;
		display: flex;
		justify-content: space-between;
		background-color: #fefefe;

		.check-b {
			width: 30%;
			padding: 0 10px;
			display: flex;
			justify-content: space-between;

			.text-b {
				flex: 1;
				margin-top: 12%;
				margin-left: 5%;
			}

			::v-deep .uni-checkbox-input {
				margin-top: 50% !important;
				border: 1px solid #6f6f7c !important;
				border-radius: 50% !important;
			}
		}

		.other {
			width: 60%;
			flex: 1;

			display: flex;
			justify-content: space-between;

			.sum {
				margin-top: 15px;
				width: 45%;

				.m {
					color: #0c34ba;
				}

				.int {
					color: #0c34ba;
					font-size: 20px;
				}

				.flo {
					color: #0c34ba;
				}

			}

			.sub {
				background-color: #0c34ba;
				color: white;
				border-radius: 20px;
				margin-top: 5px;
				width: 50%;
			}
		}
	}

	.spc {
		margin-top: 3%;
	}

	::v-deep .uni-input-input {
		color: #6f6f7c;
		background-color: #fefefe;

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