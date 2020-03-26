<template>
	<view class="container">
		<view class="top-search">
			<uni-search-bar v-model="keywords" :initialVal="initialVal" @cancel="cancel" :radius="100" bgColor="rgb(252, 252, 252)"
			 placeholder="请输入电影信息" @input="input"></uni-search-bar>
		</view>
		<view class="history content" v-if="searList.length===0">
			<view v-if="historyList.length>0">
				<view class="flex a-center j-between">
					<view class="titles">搜索历史</view>
					<image class="del-img" mode="widthFix" src="../../static/images/del.png" @click="delHistory" />
				</view>
				<view class="history-content flex a-center f-wrap">
					<view class="history-item" v-for="(item,index) in historyList" :key="index" @click="searchItem(item)">
						{{item}}
					</view>
				</view>
			</view>
			<view class="no-history flex j-center" v-else>
				暂无搜索历史
			</view>
		</view>

		<view class="content flex f-wrap" v-if="keywords.value!=''&&searList.length>0">
			<view class="content-item" v-for="(item,index) in searList" :key="index" @click="goDetails(item.id)">
				<image lazy-load="true" style="height: auto;" :src="item.cover" mode="widthFix"></image>
				<view class="name">
					{{item.name}}
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniSearchBar from '@/components/uni-search-bar/uni-search-bar.vue'
	export default {
		name: "",
		components: {
			uniSearchBar
		},
		props: {},
		data() {
			return {
				keywords: "",
				page: 1,
				pageSize: 12,
				searList: [],
				historyList: [],
				initialVal: ''

			}
		},
		methods: {
			// 点击搜索
			searchItem(item) {
				this.initialVal = item
				setTimeout(() => {
					this.initialVal = ''
				}, 1000)

			},
			// 跳转到详情页
			goDetails(id) {
				uni.navigateTo({
					url: `../details/details?id=${id}`
				});
			},
			input(e) {
				// console.log(e)
			},
			// 删除搜索历史
			delHistory() {
				uni.showModal({
					title: '是否确认删除所以搜索历史？',
					content: '',
					showCancel: true,
					cancelText: '取消',
					cancelColor: '#000000',
					confirmText: '确定',
					confirmColor: '#3CC51F',
					success: (result) => {
						if (result.confirm) {
							this.historyList = []
							wx.removeStorageSync('historyList');
						}
					},
					fail: () => {},
					complete: () => {}
				});
			},
			cancel() {
				this.keywords = {
					value: ''
				}
			},
			// 获取猜你喜欢数据
			getSearchList(e) {
				uni.showLoading()
				uni.request({
					url: `https://www.imovietrailer.com/superhero/search/list?qq=2684425594&keywords=${e}&page=${this.page}&pageSize=${this.pageSize}`,
					method: 'POST',
					data: {
						qq: 2684425594,
						type: 'superhero'
					},
					header: {
						'custom-header': 'application/json' //自定义请求头信息
					},
					success: res => {
						if (res.statusCode === 200) {
							if (res.data.data.rows.length === 0) {
								if (this.page === 1) {
									uni.showToast({
										title: '暂无此数据',
										duration: 2000,
										icon: "none"
									})
								} else {
									uni.showToast({
										title: '没有更多数据了',
										duration: 2000,
										icon: "none"
									})
								}
							} else {
								if (this.page === 1) {
									this.saveHistory(e)
								}
								this.page++;
								this.searList = this.searList.concat(res.data.data.rows)

							}

						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				})
			},
			// 存搜索历史
			saveHistory(val) {
				let arr = []
				if (uni.getStorageSync('historyList')) {
					arr = uni.getStorageSync('historyList')
					if (!JSON.stringify(arr).includes(val)) {
						arr.unshift(val)
						this.historyList.unshift(val)
						uni.setStorageSync('historyList', arr);
					}
				} else {
					arr.unshift(val)
					this.historyList.unshift(val)
					uni.setStorageSync('historyList', arr);
				}
			}
		},

		mounted() {

		},
		onLoad() {
			if (uni.getStorageSync('historyList')) {
				this.historyList = wx.getStorageSync('historyList')
			}
		},
		onReachBottom() {
			if (this.keywords.value.trim().length > 0) {
				this.getSearchList(this.keywords.value.trim())
			}
		},
		filters: {

		},
		computed: {

		},
		watch: {
			keywords(val) {
				if (val.value.trim().length > 0) {
					this.page = 1
					this.getSearchList(val.value.trim())
				} else {
					this.searList = []
				}
			}
		},
		directives: {

		},
		onNavigationBarSearchInputChanged(e) {
			console.log(e)
		}
	}
</script>

<style scoped lang="scss">
	.container {
		position: relative;
		/*  #ifdef  H5  */
		.top-search{
			padding-top: 80upx;
		}
		/*  #endif  */
		

		.top-search {
			// background-color: rgb(248, 248, 248);
			position: fixed;
			top: 0;
			left: 0;
			width: 100%;
			z-index: 9;

		}
		
		/*  #ifdef  MP-WEIXIN  */
		.top-search{
			padding-top: 0;
		}
		/*  #endif  */

		.no-history {
			margin-top: 150upx;
			font-size: 30rpx;
			color: rgb(26, 25, 25);
		}

		.history {
			width: 90%;
			margin: 0 auto;

			.titles {
				font-weight: 550;
			}

			.del-img {
				width: 50rpx;
			}

			.history-content {
				margin: 20rpx auto;
				width: 100%;

				.history-item {
					padding: 8rpx 20rpx;
					background: rgba(234, 238, 238, .8);
					border-radius: 20rpx;
					font-size: 30rpx;
					margin: 12rpx;
					color: rgb(26, 25, 25);
				}
			}

		}

		.content {
			margin-top: 100upx;

			.content-item {
				box-sizing: border-box;
				width: 33.3%;
				padding: 0 20upx;
				margin: 10upx 0;

				image {
					width: 100%;
					border-radius: 10upx;
				}

				.name {
					font-size: 30upx;
				}
			}
		}
	}
</style>
