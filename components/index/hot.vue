<template>
	<view class="container">
		
		<view class="content">
			<scroll-view class="scroll-view_H" scroll-x="true" scroll-left="120">
				<view v-for="(item,index) in hotList" :key="index" class="hot-item" @click="goDetails(item.id)">
					<image lazy-load="true" style="height: auto;" :src="item.cover" mode="widthFix"></image>
					<view class="name ellipsis">{{item.name}}</view>
					<view class="rate flex a-center j-between">
						<!-- <uni-rate :disabled="true" :value="item.score/2" size="18"></uni-rate> -->
						<sxRate :value="item.score/2"></sxRate>
						<view>{{item.score}}</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	import uniRate from '@/components/uni-rate/uni-rate.vue'
	import sxRate from '@/components/sx-rate/index.vue'
	
	export default {
		name: "",
		components: {
			sxRate,
			uniRate
		},
		props: {},
		data() {
			return {
				hotList: []
			}
		},
		methods: {
			// 跳转到详情页
			goDetails(id) {
				uni.navigateTo({
					url: `../details/details?id=${id}`
				});
			},
			getHot() {
				uni.showLoading()
				uni.request({
					url: "https://www.imovietrailer.com/superhero/index/movie/hot?qq=2684425594&type=superhero",
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
							this.hotList = res.data.data
							

						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				})
			}
		},
		mounted() {
			this.getHot()
		},
		onLoad() {

		},
		filters: {

		},
		computed: {

		},
		watch: {

		},
		directives: {

		}
	}
</script>

<style scoped lang="scss">
	

	.content {
		padding: 0 15upx;
	}

	.scroll-view_H {
		width: 100%;

		overflow: hidden;
		/* padding-right: 20rpx; */
		background: #fff;
		white-space: nowrap;
		/* border: 1px solid red; */
	}

	.scroll-view_H scroll-view {
		height: 100%;
		width: auto;
		overflow: hidden;

	}

	.hot-item {
		margin: 0 15upx;
		display: inline-block;

		image {
			width: 150upx;
		}

		.name {
			max-width: 150upx;
			font-size: 28upx;
			margin-top: 15upx;
		}
		.rate{
			font-size: 24upx;
		}
	}
</style>
