<template>
	<view class="container">
		<view class="video">
			<video id="myVideo" style="width:100%" :show-progress="true" objectFit="contain" :poster="movieDetails.cover" :src="movieDetails.trailer"></video>
		</view>
		<view class="details flex j-between">
			<view class="details-img" @click="lokeImg([movieDetails.cover],0)">
				<image style="height: auto;" :src="movieDetails.cover" mode="widthFix"></image>
			</view>
			<view class="details-content flex f-column j-between">
				<view class="details-top">
					<view class="name">
						{{movieDetails.name}}
					</view>
					<view class="basicInfo">
						{{movieDetails.basicInfo}}
					</view>
					<view class="basicInfo">
						{{movieDetails.originalName}}
					</view>
					<view class="basicInfo">
						{{movieDetails.releaseDate}}
					</view>
				</view>
				<view class="details-buttom flex j-around">
					<view class="rate-left flex j-center f-column a-center">
						<view class="total-score">
							综合评分:
						</view>
						<view class="score-number">
							{{movieDetails.score}}
						</view>
					</view>
					<view class="rate-right flex f-column j-center a-center ">
						<!-- <uni-rate disabled="true" size="15" value="3.5"></uni-rate> -->
						<sxRate :value="movieDetails.score/2"></sxRate>
						<view class="prisedCounts">
							{{movieDetails.prisedCounts}}人点赞
						</view>
					</view>

				</view>

			</view>

		</view>
		<view class="plotDesc">
			<view class="title">
				剧情介绍
			</view>
			<view class="text">
				{{movieDetails.plotDesc}}
			</view>
		</view>
		<view class="plotDesc">
			<view class="title">
				演职人员
			</view>
			<view class="content">
				<scroll-view class="scroll-view_H" scroll-x="true" scroll-left="120">
					<view class="content-item" v-for="(item,index) in roleList" :key="index">
						<image lazy-load="true" style="height: auto;" :src="item.photo" mode="widthFix" @click="lokeImg([item.photo],0)"></image>
						<view class="name ellipsis">
							{{item.name}}
						</view>
						<view class="actName ellipsis " v-if="item.role===2">
							饰演:
							{{item.actName}}
						</view>
						<view class="actName ellipsis " v-else>
							{{item.actName}}
						</view>
					</view>
				</scroll-view>
			</view>
		</view>
		<view class="plotDesc" v-if="movieDetails.plotPics">
			<view class="title">
				剧照
			</view>
			<view class="contents flex f-wrap ">
				<view class="contents-item" v-for="(item,index) in plotPics" :key="index">
					<image lazy-load="true" style="height: auto;" :src="item" mode="widthFix" @click="lokeImg(plotPics,index)"></image>
				</view>

			</view>
		</view>
	</view>
</template>

<script>
	import uniRate from '@/components/uni-rate/uni-rate.vue'
	import sxRate from '@/components/sx-rate/index.vue'
	
	export default {
		name: "",
		components: {
			uniRate,
			sxRate
		},
		props: {},
		data() {
			return {
				movieDetails: {},
				roleList: [],
				plotPics:[]
			}
		},
		methods: {
			lokeImg(url,index) {
				// 预览图片
				uni.previewImage({
					urls: url,
					current:index,
					longPressActions: {
						itemList: ['发送给朋友', '保存图片', '收藏'],
						success: function(data) {
							// console.log('选中了第' + (data.tapIndex + 1) + '个按钮,第' + (data.index + 1) + '张图片');
						},
						fail: function(err) {
							console.log(err.errMsg);
						}
					}
				});
			},

			//查询演职人员
			getRole(id, role) {
				uni.showLoading()
				uni.request({
					url: `https://www.imovietrailer.com/superhero/search/staff/${id}/${role}?qq=2684425594`,
					method: 'POST',
					data: {
						trailerId: id,
						role: role

					},
					header: {
						'custom-header': 'application/json' //自定义请求头信息
					},
					success: res => {
						if (res.statusCode === 200) {

							this.roleList = this.roleList.concat(res.data.data)
							// console.log(this.movieDetails)
						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				})
			},
			// 获取电影详细信息
			getULike(id) {
				uni.showLoading()
				uni.request({
					url: `https://www.imovietrailer.com/superhero/search/trailer/${id}?qq=2684425594`,
					method: 'POST',
					data: {
						trailerId: id,

					},
					header: {
						'custom-header': 'application/json' //自定义请求头信息
					},
					success: res => {
						if (res.statusCode === 200) {
							this.movieDetails = res.data.data
							this.plotPics=JSON.parse(this.movieDetails.plotPics)

						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				})
			}
		},
		// beforeMount() {
		// 	this.getULike()
		// },
		mounted() {

		},
		onLoad(option) {
			this.getULike(option.id)
			this.getRole(option.id, 1)
			this.getRole(option.id, 2)
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
	.container {
		background: #F7F7F7;
	}

	.details {
		margin: 50upx auto;
		width: 90%;


		.details-img {
			flex: 4;

			image {
				width: 100%;
			}
		}

		.details-content {
			margin-left: 30upx;
			flex: 5;

			.details-top {
				.name {
					font-size: 40upx;
					font-weight: 550;
				}

				.basicInfo {
					font-size: 26upx;
					margin-top: 5px;
					color: #808080;
				}
			}

			.details-buttom {
				margin-top: 30upx;
				flex: 1;
				background: white;

				.rate-left {
					flex: 1;

					.total-score {
						font-size: 38upx;
					}

					.score-number {
						font-size: 30upx;
						color: #ff7010;
					}
				}

				.rate-right {
					flex: 1;
					padding-top: 20upx;
					;

					.prisedCounts {
						font-size: 26upx;
						margin-top: 20upx;
						color: #808080;
					}
				}
			}

		}
	}

	.plotDesc {
		border-top: #808080 solid 2upx;
		width: 90%;
		margin: 10upx auto;
		font-size: 32upx;
		padding-bottom: 20upx;

		.title {
			margin: 20upx 0;
			font-size: 30upx;
			color: #808080;
		}

		.content {
			.scroll-view_H {
				width: 100%;

				overflow: hidden;
				/* padding-right: 20rpx; */
				// background: #fff;
				white-space: nowrap;
				/* border: 1px solid red; */
			}

			.scroll-view_H scroll-view {
				height: 100%;
				width: auto;
				overflow: hidden;

			}

			.content-item {
				width: 130upx;
				margin: 0 20upx;
				display: inline-block;

				image {
					width: 100%;
				}

				.name {
					margin: 10upx 0;
					font-size: 30upx;
				}

				.actName {
					margin: 10upx 0;
					font-size: 28upx;
					color: #808080;
					-webkit-line-clamp: 1;
				}
			}
		}

		.contents {
			.contents-item {
				width: 150upx;
				margin: 30upx;

				image {
					width: 100%;
					min-height: 168upx;
				}
			}
		}
	}
</style>
