<template>
	<view>
		<view class="title flex a-center">
			<image lazy-load="true" style="height: auto;" mode="widthFix" :src="iconPath"></image>
			{{title}}
		</view>
	</view>
</template>

<script>
	export default {
		name: "",
		components: {

		},
		props: {
			title: {
				style: String,
				default: "热门超英"
			},
			iconPath: {
				style: String,
				default: "../../static/images/hot.png"
			}
		},
		data() {
			return {}
		},
		methods: {

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
	.title {
		padding: 30upx;
		font-size: 34upx;
		font-weight: 550;

		image {
			width: 50upx;
			margin-right: 10upx;
		}
	}
</style>
