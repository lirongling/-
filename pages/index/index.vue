<template>
	<view class="content">
		<Wheel></Wheel>
		<HomeTitle></HomeTitle>
		<Hot></Hot>
		<HomeTitle title="热门预告" iconPath="../../static/images/foreshow.png"></HomeTitle>
		<Trailer></Trailer>
		<HomeTitle title="猜你喜欢" iconPath="../../static/images/ulike.png"></HomeTitle>
		<ULike></ULike>
	</view>
</template>

<script>
	import Wheel from "../../components/index/wheel.vue"
	import Hot from "../../components/index/hot.vue"
	import Trailer from "../../components/index/trailer.vue"
	import ULike from "../../components/index/uLike.vue"
	import HomeTitle from "../../components/index/homeTitle.vue"
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		components: {
			Wheel,
			Hot,
			Trailer,
			ULike,
			HomeTitle
		},
		onLoad() {

		},
		methods: {

		}
	}
</script>

<style>
	.content {}
</style>
