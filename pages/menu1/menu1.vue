<template>
	<view>
		<view class="menu">
			<uni-search-bar placeholder="输入商品关键字..." class="search" radius="18" v-model="search_val">
				<uni-icons slot="searchIcon" color="#999999" size="18" type="home" />
			</uni-search-bar>
		</view>
		<view class="menu-type">
			<view class="type-list" v-for="item in typeMenu" :key="item.id"
				@click="getTypeProducts(item.id,item.key,item.type)">
				<image :src="item.isActive ? item.activeImg:item.img" mode="widthFix" class="list-pic"></image>
				<view :class="[item.isActive ? 'active' :'']" class="list-name">
					{{item.typeDesc}}
				</view>
			</view>
		</view>
		<view class="total-i">
			<view class="menu-info" v-for="(item,index) in typeproduct" @click="sendinfo(item)">
				<view class="t-pic">
					<image :src="item.smallImg" mode="widthFix" class="imag"></image>
				</view>
				<view class="name-i">
					<view class="znname">
						{{item.name}}
					</view>
					<view class="enname">
						{{item.enname}}
					</view>
				</view>
				<view class="price-t">
					￥{{item.price}}
				</view>

			</view>
		</view>
	</view>
</template>

<script>
	export default {
		onShow() {
			this.getval()
		},
		data() {
			return {
				search_val: "",
				typeMenu: [{
						img: "../../static/menuIcon/icons_01.png", //未选中
						activeImg: "../../static/menuIcon/icons_02.png", //选中之后
						isActive: false, //表示没有被选中
						key: 'isHot' //属于哪一项
					},
					{
						img: "../../static/menuIcon/icons_03.png", //未选中
						activeImg: "../../static/menuIcon/icons_04.png", //选中之后
						isActive: false, //表示没有被选中
						key: 'type' //属于哪一项
					},
					{
						img: "../../static/menuIcon/icons_05.png", //未选中
						activeImg: "../../static/menuIcon/icons_06.png", //选中之后
						isActive: false, //表示没有被选中
						key: 'type' //属于哪一项
					},
					{
						img: "../../static/menuIcon/icons_07.png", //未选中
						activeImg: "../../static/menuIcon/icons_08.png", //选中之后
						isActive: false, //表示没有被选中
						key: 'type' //属于哪一项
					},
					{
						img: "../../static/menuIcon/icons_09.png", //未选中
						activeImg: "../../static/menuIcon/icons_10.png", //选中之后
						isActive: false, //表示没有被选中
						key: 'type' //属于哪一项
					}
				],
				typeproduct: []
			};
		},
		onShow() {
			this.gettype()
			//默认显示第一项
			this.getTypeProducts(0, "isHot", 1)
		},
		methods: {
			getval() {
				let str = uni.getStorageSync('search_val')
				this.search_val = str
				console.log(str)
			},
			gettype() {
				uni.request({
					url: "http://www.kangliuyong.com:10002/type",
					method: "get",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA="
					},
					success: (res) => {
						// console.log(res);
						if (res.data.code === 400) {
							let hot = {
								id: 0,
								typeDesc: "推荐",
								type: 1
							}
							res.data.result.unshift(hot)
							// console.log(res);
						}
						let arr = []
						var obj = {}
						//遍历数组类似v-for
						res.data.result.forEach((item, index) => {
							// console.log(index, item);
							obj = {
								...item,
								...this.typeMenu[index]
							}
							// console.log(obj);
							arr.push(obj)
						})
						//复制到data
						this.typeMenu = arr
					}
				})
			},
			getTypeProducts(index, key, value) {
				uni.request({
					url: "http://www.kangliuyong.com:10002/typeProducts",
					method: "",
					data: {
						appkey: "U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						key,
						value
					},
					success: (res) => {
						if (res.data.code === 500) {
							this.typeproduct = res.data.result
							console.log(this.typeproduct);
							// for (var i = 0; i < 5; i++) {
							// 	if (i === index) {
							// 		// console.log("哈哈哈");
							// 		this.typeMenu[i].isActive = true
							// 	}
							// 	this.typeMenu[i].isActive = false
							// }
							this.typeMenu.forEach(item => {
								item.isActive = false;
							})
							this.typeMenu[index].isActive = true
						}
					}
				})
			},
			sendinfo(val) {
				uni.setStorageSync('infomation', val)
				//在起始页面跳转到test.vue页面并传递参数
				uni.navigateTo({
					url: '../infomation/infomation'
				});
			}
		}
	}
</script>

<style lang="scss">
	.menu {
		width: 100%;
		display: flex;
		justify-content: space-between;
		/* padding: 3px; */
		background-color: #fff;

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

	.menu-type {
		display: flex;
		justify-content: space-evenly;
		background-color: #fbfbfb;
		margin-top: 20px;

		.type-list {
			padding: 20px 0;


			.list-name {
				font-size: 18px;
				font-weight: bold;
				text-align: center;
			}
		}
	}

	.list-pic {
		width: 60px;

	}

	.total-i {
		padding: 10px;
	}

	.menu-info {
		width: 96%;
		display: flex;
		padding: 10px;
		margin-top: 10px;
		justify-content: space-between;
		border-radius: 10px;
		background-color: #ffffff;

		.t-pic {
			width: 30%;

			.imag {
				width: 100%;
				height: 70%;
				border-radius: 7px;
			}
		}

		.name-i {
			width: 10%;
			display: flex;
			flex-direction: column;
			text-align: left;
			flex: 1;

			.znname {
				width: 100%;
				height: 30%;
				color: #6f6f7c;
				margin-top: 20%;
				font-size: 20px;
				margin-left: 10px;
				overflow: hidden;
				white-space: nowrap;
				text-overflow: ellipsis;
			}

			.enname {
				width: 100%;
				height: 30%;
				margin-left: 10px;
				color: #6f6f7c;
				margin-top: 0;
				overflow: hidden;
				white-space: nowrap;
				text-overflow: ellipsis;
			}
		}

		.price-t {
			width: 32%;
			text-align: right;
			font-weight: bold;
			margin-top: 10%;
			margin-right: 8%;
			color: #0c34ba;
		}
	}
</style>