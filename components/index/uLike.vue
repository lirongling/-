<template>
	<view class="container">
		<view class="like-item flex a-center" v-for="(item,index) in uLikeList" :key="index" @click="goDetails(item.id)">
			<view class="item-left flex a-center ">
				<view class="item-img">
					<image lazy-load="true" style="height: auto;" :src="item.cover" mode="widthFix"></image>
				</view>
				<view class="item-details">
					<view class="name">
						{{item.name}}
					</view>
					<view class="rate flex a-center ">
						<uni-rate :disabled="true" :value="item.score/2" size="12"></uni-rate>
						<!-- <view>{{item.score}}</view> -->
					</view>
					<view class="basicInfo">
						{{item.basicInfo}}
					</view>
					<view class="releaseDate">
						{{item.releaseDate}}
					</view>
				</view>
			</view>
			<view class="item-right flex a-center j-center f-column">
               <image src="../../static/images/like.png" mode="widthFix"></image>
			   <view class="text">
			   	赞一赞
			   </view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniRate from '@/components/uni-rate/uni-rate.vue'
	export default {
		name: "",
		components: {
			uniRate
		},
		props: {},
		data() {
			return {
				uLikeList: []
			}
		},
		methods: {
			// 跳转到详情页
			goDetails(id) {
				uni.navigateTo({
					url: `../details/details?id=${id}`
				});
			},
			// 获取猜你喜欢数据
			getULike() {
				uni.showLoading()
				uni.request({
					url: "https://www.imovietrailer.com/superhero/index/guessULike?qq=2684425594",
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
							this.uLikeList = res.data.data
							console.log(this.uLikeList)
						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				})
			}
		},
		beforeMount() {
			this.getULike()
		},
		mounted() {

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
	.like-item {
		padding: 15upx 20upx;

		.item-left {
			flex: 3;
             border-right:#808080 dashed 2upx ;
			 padding-right: 10upx;
			.item-img {
				flex: 1;

				image {
					width: 100%;
					border-radius: 10upx;
				}
			}

			.item-details {
				flex: 2;
				margin-left: 10upx;

				.name {
					font-size: 32upx;
				}
				.rate{
					font-size: 25upx;
					margin-top: 20upx;
				}
				.basicInfo{
					font-size: 28upx;
					margin-top: 10upx;
					color: #808080;
				}
				.releaseDate{
					font-size: 28upx;
					// margin-top: 10upx;
					color: #808080;
				}
				
			}
		}

		.item-right {
			flex: 1;
			height: 100%;
			margin-left: 10upx;
			color: #FFBE34;
			font-size: 30upx;
			image{
				width: 60upx;
				margin-bottom: 10upx;
			}
		}
	}
</style>
