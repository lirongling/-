<template>
	<view class="content">
		<swiper class="swiper" :circular="true" :indicator-dots="true" indicator-color="rgba(0,0,0,.3)" :autoplay="true" interval="5000"
		 duration="1000">
			<swiper-item class="image-item" v-for="(item,index) in carouselList" :key="index">
				<image lazy-load="true" style="height: auto;" :src="item.image" class="img" mode="widthFix"></image>
			</swiper-item>
		</swiper>
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
				carouselList: []
			}
		},
		methods: {
			getCarousel() {
				uni.showLoading()
				uni.request({
					url: "https://www.imovietrailer.com/superhero/index/carousel/list?qq=2684425594",
					method: 'POST',
					data: {

					},
					header: {
						'custom-header': 'application/x-www-form-urlencoded' //自定义请求头信息
					},
					success: res => {
					
						if (res.statusCode === 200) {
							this.carouselList = res.data.data
							
							
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
           this.getCarousel()
		},
		onLoad() {
           this.getCarousel()
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
	.swiper{
		width: 100vw;
	}
	.image-item {
		width: 100%;
		overflow: hidden;
		
	}

	.img {
		width: 100%;
		
	}
</style>
