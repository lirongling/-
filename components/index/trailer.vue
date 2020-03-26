<template>
	<view class="container flex a-center f-wrap">
	
		<view class="trailer-item flex a-center f-column" v-for="(item,index) in trailerList" :key="index">
			<!-- <image :src="item.cover"></image>
			<view class="initial"></view> -->
			<view class="video-container">
				<video   :id="getVideoId(index)" style="width:100%" @play="play(index)" :show-progress="true" objectFit="contain"
				 :poster="item.cover" :src="item.trailer"></video>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: "",
		components: {

		},
		props: {},
		data() {
			return {
				trailerList: [],
				playVidio: null,
			}
		},
		methods: {
			stop() { 
				console.log('222')
				this.playVidio.pause()
			},
			getVideoId(index) {
				return `myVideo${index}`
			},
			play(index) {
				let a = `myVideo${index}`
				let newVideo = uni.createVideoContext(a);
				console.log(newVideo)
				if (this.playVidio && this.playVidio.id !== newVideo.id) {
				
					newVideo.play()
					this.playVidio.pause()
				}
				this.playVidio = newVideo
				
			},
			getTrailer() {
				uni.showLoading()
				uni.request({
					url: "https://www.imovietrailer.com/superhero/index/movie/hot?qq=2684425594&type=trailer",
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
							this.trailerList = res.data.data
							
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
			this.getTrailer()
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
	.container {
		.trailer-item {
			width: 50%;
			padding: 10upx 0;

			.video-container {
				width: 93%;
				
				overflow: hidden;

				/deep/.uni-video {
					width: 500rpx !important;
				}
			}
		}
	}
</style>
