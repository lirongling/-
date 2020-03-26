<template>
	<view class="container">
		<view class="login no-login flex a-center" v-if="userInfo">
			<view class="content flex a-center">
				<!-- 头像 -->
				<view class="avatar" @click="userDetails">
					<image :src="userInfo.faceImage" mode="widthFix"></image>
				</view>
				<view class="login" @click="userDetails">
					<view v-if="userInfo.nickname">
						{{userInfo.nickname}}
					</view>
					<view v-else>
						null
					</view>
				</view>
			</view>
		</view>
		<view class="no-login flex a-center" v-else>
			<view class="content flex a-center">
				<!-- 头像 -->
				<view class="avatar">
					<image src="https://i.loli.net/2017/08/21/599a521472424.jpg" mode="widthFix"></image>
				</view>
				<view class="login" @click="goLogin">
					登录/注册
				</view>
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
			return {}
		},
		methods: {
			// 登录
			goLogin() {
				uni.navigateTo({
					url: "../login/login"
				})
			},
			// 用户详情
			userDetails(){
				uni.navigateTo({
					url:"../userDeails/userDeails"
				})
			}
			
		},
		mounted() {

		},
		onLoad() {
			
			
		},
		filters: {

		},
		computed: {
			userInfo() {
				return this.$store.state.userInfo
			}
		},
		watch: {

		},
		directives: {

		}
	}
</script>

<style scoped lang="scss">
	.no-login {
		width: 100%;
		height: 300upx;
		background: linear-gradient(to right, #FFD655, #ff9830);
		padding: 0 30upx;

		.content {
			margin-top: 80upx;

			.avatar {
				width: 120upx;
				height: 120upx;
				border-radius: 50%;
				overflow: hidden;

				image {
					width: 100%;
					
				}
			}

			.login {
				margin-left: 40upx;
				font-size: 40upx;
				font-weight: 550;
			}
		}
	}
</style>
