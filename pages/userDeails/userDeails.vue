<template>
	<view class="container">
		<view class="content">
			<view class="content-item flex a-center j-between">
				<view class="item-left">
					头像
				</view>
				<view class="item-right flex a-center">
					<image class="img" :src="userInfo.faceImage" mode="heightFix" @click="chooseImage">
					</image>
					<image class="more" src="../../static/images/right.png" mode="widthFix"></image>
				</view>
			</view>
			<view class="content-item flex a-center j-between">
				<view class="item-left">
					昵称
				</view>
				<view class="item-right flex a-center">
					<input type="text" @blur="blur" :value="userInfo.nickname" />
					<image class="more" src="../../static/images/right.png" mode="widthFix"></image>
				</view>
			</view>
			<view class="content-item flex a-center j-between">
				<view class="item-left">
					生日
				</view>
				<view class="item-right flex a-center">
					<timeSelector showType="date" beginDate="1970-01-01" :endDate="endDate" @btnConfirm="btnConfirm">
						<view class="flex a-center">
							{{userInfo.birthday}}
							<image class="more" src="../../static/images/right.png" mode="widthFix"></image>
						</view>
					</timeSelector>

				</view>
			</view>
			<view class="content-item flex a-center j-between">
				<view class="item-left">
					性别
				</view>
				<view class="item-right flex a-center">
					<radio-group @change="radioChange" class="flex a-center">
						<label class="uni-list-cell uni-list-cell-pd flex a-center" v-for="(item, index) in items" :key="item.value">
							<view>
								<radio :value="item.value" :checked="index === current" />
							</view>
							<view>{{item.name}}</view>
						</label>
					</radio-group>
					<image class="more" src="../../static/images/right.png" mode="widthFix"></image>
				</view>
			</view>
		</view>
		<view class="btn">
			<button @click="loginout">退出登录</button>
			<button @click="loginout">清理缓存</button>
		</view>
	</view>
</template>

<script>
	import uploadImg from "../../components/amazarashi-uploadimg/uploadImg.vue"
	import uniCalendar from '@/components/uni-calendar/uni-calendar.vue'
	import uniPopup from "@/components/uni-popup/uni-popup.vue"
	import timeSelector from '@/components/wing-time-selector/wing-time-selector.vue'
	export default {
		name: "",
		components: {
			uploadImg,
			uniCalendar,
			uniPopup,
			timeSelector
		},
		props: {},
		data() {
			return {
				endDate: '',
				birthday: '',
				current: 0,
				items: [{
						value: '0',
						name: '未知'
					},
					{
						value: '1',
						name: '男'
					},
					{
						value: '2',
						name: '女'
					}
				],
			}
		},
		methods: {
			// 退出登录
			loginout() {
				uni.showLoading()
				uni.request({
					url: `https://www.imovietrailer.com/superhero/user/logout?qq=2684425594&&userId=${this.$store.state.userInfo.id}`,
					method: 'POST',
					data: {
						// trailerId: id,

					},
					header: {
						'custom-header': 'application/json' //自定义请求头信息
					},
					success: res => {
						if (res.statusCode === 200) {
							uni.showToast({
								title:"退出成功"
							})
							uni.removeStorageSync('userInfo')
							this.$store.state.userInfo=null
							uni.switchTab({
								url:"../index/index"
							})

						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				})
			},
			radioChange(e) {
				if (this.$store.state.userInfo.sex !== Number(e.target.value)) {
					this.$store.state.userInfo.sex = Number(e.target.value)
					this.editUser()
				}
			},
			btnConfirm(e) {
				if (this.$store.state.userInfo.birthday !== e.key) {
					this.$store.state.userInfo.birthday = e.key
					this.editUser()
				}
			},
			// 上传图片
			chooseImage() {
				uni.chooseImage({
					success: (chooseImageRes) => {
						const tempFilePaths = chooseImageRes.tempFilePaths;
						uni.uploadFile({
							url: `https://www.imovietrailer.com/superhero/user/uploadFace?qq=2684425594&userId=${this.$store.state.userInfo.id}`, //仅为示例，非真实的接口地址
							filePath: tempFilePaths[0],
							name: 'file',
							formData: {
								'user': 'test'
							},
							header: {
								"headerUserId": this.$store.state.userInfo.id,
								"headerUserToken": this.$store.state.userInfo.userUniqueToken
							},
							success: (uploadFileRes) => {
								let res = JSON.parse(uploadFileRes.data)
								if (res.status === 200) {
									uni.showToast({
										title: "上传成功"
									})
									uni.setStorageSync('userInfo', res.data)
									this.$store.state.userInfo = res.data

								}
							}
						});
					}
				});
			},
			blur(e) {
				if (this.$store.state.userInfo.nickname !== e.detail.value) {
					this.$store.state.userInfo.nickname = e.detail.value
					this.editUser()
				}
			},
			// 修改用户信息
			editUser() {
				uni.showLoading()
				uni.request({
					url: `https://www.imovietrailer.com/superhero/user/modifyUserinfo?qq=2684425594`,
					method: 'POST',
					data: {
						userBO: this.$store.state.userInfo,
						birthday: this.$store.state.userInfo.birthday,
						nickname: this.$store.state.userInfo.nickname,
						sex: this.$store.state.userInfo.sex,
						userId: this.$store.state.userInfo.id,
					},
					header: {
						"headerUserId": this.$store.state.userInfo.id,
						"headerUserToken": this.$store.state.userInfo.userUniqueToken,
						'custom-header': 'application/json' //自定义请求头信息
					},
					success: res => {
						if (res.statusCode === 200) {
							uni.showToast({
								title: "修改成功"
							})
							uni.setStorageSync('userInfo', res.data.data)
							this.$store.state.userInfo = res.data.data

						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				})
			},

		},
		mounted() {

		},
		onLoad() {
			// 获取当前日期
			this.endDate = this.$dayjs(new Date()).format('YYYY-MM-DD')
			if (this.$store.state.userInfo.sex) {
				this.current = this.$store.state.userInfo.sex
			} else {
				this.current = 0
			}
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
	.container {
		.content {
			margin-top: 60upx;

			.content-item {
				width: 90%;
				margin: 30upx auto;

				.item-left {
					font-size: 37upx;
					color: #808080;
				}

				.item-right {
					font-size: 36upx;
					color: #808080;

					input {
						text-align: right;
					}

					.img {
						width: 80upx;
						height: 80upx;
						overflow: hidden;
						border-radius: 50%;
					}

					.more {
						width: 40upx;
					}
				}
			}
		}

		.btn {
			width: 80%;
			margin: 0 auto;
			position: fixed;
			bottom: 0;
			left: 50%;
			transform: translateX(-50%);

			button {
				margin: 50upx 0;
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
</style>
