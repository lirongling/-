<template>
	<view class="container flex a-center f-column">

		<view class="avatar">
			<image src="https://i.loli.net/2017/08/21/599a521472424.jpg" mode="widthFix"></image>
		</view>
		<view class="from">
			<view class="from-item flex a-center j-center">
				<input v-model="user.username" type="text" value="" placeholder="请输入用户名" />
			</view>
			<view class="from-item flex a-center j-center">
				<input v-model="user.password" type="text" :password="password" value="" placeholder="请输入密码" />
				<view class="icon flex a-center" @click.stop="visible=!visible;password=!password">
					<image src="../../static/images/visible.png" mode="widthFix" v-if="visible"></image>
					<image src="../../static/images/invisible.png" mode="widthFix" v-else></image>
				</view>
			</view>
			<view class="btn">
				<button @click="login">注册/登录</button>
			</view>
		</view>
		<!-- #ifndef H5 -->
		<view class="other ">
			<view class="other-title">
				第三方登录
			</view>
			<view class="other-content flex a-center j-center">
				<!-- #ifdef MP-WEIXIN -->
				<view class="other-item" @click="weChatLogin">
					<image src="../../static/images/weChat.png" mode="widthFix"></image>
				</view>
				<!-- #endif -->
				<!-- #ifdef APP-PLUS -->
				<view class="other-item" @click="otherLogin('weixin')">
					<image src="../../static/images/weChat.png" mode="widthFix"></image>
				</view>
				<view class="other-item" @click="otherLogin('qq')">
					<image src="../../static/images/qq.png" mode="widthFix"></image>
				</view>
				<view class="other-item" @click="otherLogin('sinaweibo')">
					<image src="../../static/images/weibo.png" mode="widthFix"></image>
				</view>
				<!-- #endif -->
			</view>
		</view>
		<!-- #endif -->
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
				visible: true,
				password: true,
				user: {
					username: '',
					password: ''
				}
			}
		},
		methods: {
			//#ifdef MP-WEIXIN
			// 微信小程序登录
			weChatLogin() {
				let _this = this;
				uni.getUserInfo({
					provider: 'weixin',
					success: function(infoRes) {
						console.log(infoRes.userInfo)
						uni.login({
							success: function(res) {
								// 获取code  
								uni.setStorageSync('isCanUse', false); //记录是否第一次授权  false:表示不是第一次授权
								// console.log(res.code, infoRes.userInfo)
								_this.weixinLogin(res.code, infoRes.userInfo);
							}
						});


					},
					fail(res) {}
				});
			},
			weixinLogin(code, e) {
				console.log('1111')
				uni.showLoading()
				uni.request({
					url: `https://www.imovietrailer.com/superhero/stu/mpWXLogin/${code}?qq=2684425594`,
					method: 'POST',
					data: {
						wxUserBO: e,
						avatarUrl: e.avatarUrl,
						nickName: e.nickName,
						whichMP: 1,
						// qq:'434714873'

					},
					header: {
						'custom-header': 'application/json' //自定义请求头信息
					},
					success: res => {
						console.log(res)
						if (res.statusCode === 200) {
							// this.movieDetails = res.data.data

						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				})
			},
			//#endif
			//#ifdef APP-PLUS
			otherLogin(e) {
				var self = this;
				console.log(e)
				uni.login({
					provider: e,
					success: (res) => {
						uni.getUserInfo({
							provider: e,
							success: function(infoRes) {
								console.log(infoRes)
								self.otherLogins(e,infoRes.userInfo)
								// let formdata = {
								// 	nickName: infoRes.userInfo.nickName,
								// 	gender: infoRes.userInfo.gender,
								// 	openId: infoRes.userInfo.openId,
								// 	unionId: infoRes.userInfo.unionId
								// };
								// self.$go.post("/wxlogin", formdata).then(res => {});
							}
						})
					},
					fail: (err) => {}
				});

			},
			otherLogins(type,e) {
				uni.showLoading()
				uni.request({
					url: `https://www.imovietrailer.com/superhero/appUnionLogin/${type}?qq=2684425594`,
					method: 'POST',
					data: {
						appLoginUserBO: e,
						face: e.avatarUrl,
						nickName: e.nickName,
						openIdOrUid: e.openId,
						qq:'434714873'
					},
					header: {
						'custom-header': 'application/json' //自定义请求头信息
					},
					success: res => {
						console.log(res)
						if (res.statusCode === 200) {
							uni.setStorageSync('userInfo', res.data.data)
							console.log(res.data.data)
							this.$store.state.userInfo = res.data.data
							uni.switchTab({
								url: '../my/my'
							})
							uni.showToast({
								title: '登录/注册成功'
							})

						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				})
			},
			//#endif
			//h5注册登录
			login() {
				uni.showLoading()
				uni.request({
					url: `https://www.imovietrailer.com/superhero/user/registOrLogin?qq=2684425594`,
					method: 'POST',
					data: {
						username: this.user.username,
						password: this.user.password,
						userBO: this.user

					},
					header: {
						'custom-header': 'application/json' //自定义请求头信息
					},
					success: res => {

						if (res.statusCode === 200) {
							if (res.data.status === 200) {
								uni.setStorageSync('userInfo', res.data.data)
								console.log(res.data.data)
								this.$store.state.userInfo = res.data.data
								uni.switchTab({
									url: '../my/my'
								})
								uni.showToast({
									title: '登录/注册成功'
								})
							} else {

								uni.showToast({
									title: res.data.msg,
									icon: "none"
								})
							}
							// console.log(res)
							// this.movieDetails = res.data.data

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
		.avatar {
			margin-top: 160upx;
			width: 170upx;
			border-radius: 50%;

			image {
				width: 100%;
				border-radius: 50%;
			}
		}

		.from {
			margin: 30upx auto;
			width: 100%;

			.from-item {
				margin: 40upx auto;
				border-radius: 40upx;
				width: 80%;
				height: 80upx;
				box-shadow: 0 1px 6px rgba(0, 0, 0, .2);
				border-color: #eee;
				position: relative;

				.icon {
					position: absolute;
					right: 20upx;
					top: 50%;
					transform: translateY(-50%);
					height: auto;
					width: 50upx;
					height: 50upx;

					image {
						width: 50upx;
					}
				}
			}

			.btn {
				width: 80%;
				margin: 0 auto;
				margin-top: 80upx;

				button {
					border-radius: 50upx;
					background-color: #636363;
					color: white;
					box-shadow: 0 2upx 20upx rgba(0, 0, 0, .2);
					border-color: #eee;
				}

				button:active {
					background: #7d7d7d;
					border-color: #f0f0f0;
					color: #f4f4f4;
				}

			}
		}

		.other {
			margin: 50upx auto;

			.other-title {
				margin-bottom: 50upx;
				font-size: 30upx;
				color: #808080;
			}

			.other-item {
				width: 80upx;
				margin: 10upx 20upx;

				image {
					width: 100%;
				}
			}
		}
	}
</style>
