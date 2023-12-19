<template>
	<view class="totals">
		<view class="head">
			<view class="back">
				<uni-icons type="back" size="30" color="#0c34ba"></uni-icons>
				<view class="back-text spc" @click="gotoback">
					<text class="clickBack">返回</text>
				</view>
			</view>
		</view>
		<view class="backcolor" v-show="addressList.length !== 0"></view>
		 <view class="content">
			 <button>新增</button>
			<!-- <view v-if="showInput">
				<input v-model="name" placeholder="姓名" />
				<input v-model="tel" placeholder="电话" />
				<input v-model="province" placeholder="省份" />
				<input v-model="city" placeholder="市" />
				<input v-model="county" placeholder="区县" />
				<input v-model="addressDetail" placeholder="详细地址" />
				<input v-model="areaCode" placeholder="地区编号" />
				<input v-model="postalCode" placeholder="邮政编码" />
				<input v-model="isDefault" placeholder="默认地址，0或1" />
				<button @click="addAddress">提交</button>
			</view> -->
			 <view v-if="addressList.length !== 0" class="address-box">
				<view v-for="(address, index) in addressList" :key="index" class="address-card">
					<view class="address-info" v-if="!address.editing">
						<view class="name-tel">
							<view>{{ address.name }}{{ address.tel }}</view>
							<view class="default-box" :style="{ backgroundColor: address.isDefault ? '#0b33ba' : '' }">
								<text class="default-txt">{{ address.isDefault ? '默认' : '' }}</text>
							</view>
						</view>
						<view>{{ address.province }}{{ address.city }}{{ address.county }}{{ address.addressDetail }}
						</view>
					</view>
					<view v-if="address.editing">
						<input v-model="address.name" placeholder="姓名" />
						<input v-model="address.tel" placeholder="电话" />
						<input v-model="address.province" placeholder="省份" />
						<input v-model="address.city" placeholder="市" />
						<input v-model="address.county" placeholder="区县" />
						<input v-model="address.addressDetail" placeholder="详细地址" />
						<input v-model="address.areaCode" placeholder="地区编号" />
						<input v-model="address.postalCode" placeholder="邮政编码" />
						<input v-model="address.isDefault" placeholder="默认地址，0或1" />
					</view>
					<view v-if="!address.editing" class="edit-btn" @click="editAddress(index)">编辑</view>
					<view v-if="address.editing" class="save-btn" @click="saveAddress(index)">保存</view>
					<view v-if="!address.editing" class="delete-btn" @click="deleteAddress(address.aid)">删除</view>
					<view v-if="address.editing" class="cancel-btn" @click="cancelEdit(index)">取消</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				name: "",
				tel: "",
				province: "",
				city: "",
				county: "",
				addressDetail: "",
				areaCode: "",
				postalCode: "",
				isDefault: "",
				addressList: [], // 存放获取到的地址列表
				showInput: false // 控制显示输入框的标志位
			};
		},
		methods: {
			gotoback() {
				uni.switchTab({
					url: "../my/my"
				});
			},
			addAddress() {
				uni.request({
					url: "http://www.kangliuyong.com:10002/addAddress",
					method: "POST",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString: uni.getStorageSync("token"),
						name: this.name,
						tel: this.tel,
						province: this.province,
						city: this.city,
						county: this.county,
						addressDetail: this.addressDetail,
						areaCode: this.areaCode,
						postalCode: this.postalCode,
						isDefault: this.isDefault
					},
					header: {
						"Content-Type": "application/x-www-form-urlencoded;charset=utf-8"
					},
					success: res => {
						console.log(res.data);
						this.getAddressList(); // Refresh the address list immediately
						this.toggleInput();
						this.clearInputs();
					},
					fail: err => {
						// 请求失败，处理错误信息
						// ...
					}
				});
			},
			getAddressList() {
				uni.request({
					url: "http://www.kangliuyong.com:10002/findAddress",
					method: "GET",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString: uni.getStorageSync("token")
					},
					success: res => {
						console.log(res.data.result);
						const addressList = res.data.result.map(address => ({
							...address,
							editing: false
						}));
						// 将默认地址放在第一位
						const defaultAddress = addressList.find(address => address.isDefault === 1);
						if (defaultAddress) {
							addressList.splice(addressList.indexOf(defaultAddress), 1);
							addressList.unshift(defaultAddress);
						}
						this.addressList = addressList;
					},
					fail: err => {
						// 请求失败，处理错误信息
						// ...
					}
				});
			},
			toggleInput() {
				this.showInput = !this.showInput;
			},
			editAddress(index) {
				this.addressList[index].editing = true;
			},
			cancelEdit(index) {
				this.getAddressList();
			},
			saveAddress(index) {
				const address = this.addressList[index];
				// 保存编辑框中的数据
				const editedAddress = {
					aid: address.aid,
					name: this.name,
					tel: this.tel,
					province: this.province,
					city: this.city,
					county: this.county,
					addressDetail: this.addressDetail,
					areaCode: this.areaCode,
					postalCode: this.postalCode,
					isDefault: this.isDefault
				};
				uni.request({
					url: "http://www.kangliuyong.com:10002/editAddress",
					method: "POST",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString: uni.getStorageSync("token"),
						...editedAddress
					},
					header: {
						"Content-Type": "application/x-www-form-urlencoded;charset=utf-8"
					},
					success: res => {
						console.log(res.data);
						// 将保存的编辑框数据写入addressList数组中
						this.addressList[index] = {
							...address,
							...editedAddress,
							editing: false
						};
						this.getAddressList();
					},
					fail: err => {
						// 请求失败，处理错误信息
						// ...
					},
					complete: () => {
						// 清空编辑框数据
						this.clearInputs();
					}
				});
			},
			deleteAddress(aid) {
				uni.request({
					url: "http://www.kangliuyong.com:10002/deleteAddress",
					method: "POST",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString: uni.getStorageSync("token"),
						aid
					},
					header: {
						"Content-Type": "application/x-www-form-urlencoded;charset=utf-8"
					},
					success: res => {
						console.log(res.data);
						this.getAddressList();
					},
					fail: err => {
						// 请求失败，处理错误信息
						// ...
					}
				});
			},
			clearInputs() {
				this.name = "";
				this.tel = "";
				this.province = "";
				this.city = "";
				this.county = "";
				this.addressDetail = "";
				this.areaCode = "";
				this.postalCode = "";
				this.isDefault = "";
			}
		},
		mounted() {
			this.getAddressList();
		}
	};
</script>


<style lang="scss">
	.totals {
		background-color: #f7f7f7;

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
		}
		
		.clickBack{
			vertical-align: -5rpx;
		}

		.backcolor {
			display: flex;
			width: 100%;
			height: 150px;
			background-image: url(../../static/cart-t.jpg);
			background-size: cover;
			background-repeat: no-repeat;
			background-position: center center;
		}

		.content {
			padding: 20px;
			background-color: #ffffff;
			z-index: 1;
			border-radius: 8px;

			button {
				width: 100%;
				height: 40px;
				background-color: #0c34ba;
				color: #fff;
				border: none;
				border-radius: 5px;
				cursor: pointer;
			}

			.address-card {
				display: flex;
				flex-direction: column;
				justify-content: space-between;
				padding: 10px;
				margin-top: 10px;
				background-color: #ffffff;
				box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
				margin-top: 10px;
				margin-bottom: 10px;
			}

			.name-tel {
				display: flex;
				justify-content: space-between;
				align-items: center;
			}

			.default-box {
				display: flex;
				align-items: center;
				justify-content: center;
				width: 40px;
				height: 20px;
				border-radius: 10px;
			}

			.default-txt {
				color: #fff;
				font-size: 12px;
			}

			.address-info {
				flex-grow: 1;
			}

			.edit-btn {
				color: #0c34ba;
				cursor: pointer;
			}

			.delete-btn {
				color: #ff0000;
				cursor: pointer;
			}
		}
	}
</style>