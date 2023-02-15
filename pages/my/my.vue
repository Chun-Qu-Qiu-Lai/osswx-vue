<template>
	<view class="max" style="">
		<cu-custom bgColor="#ffffff">
			<block slot="content">我的</block>
		</cu-custom>
		<view style="height: 300rpx;width: 80%;padding: 20px 0px;
			display: flex;text-align: center;
			align-items: center;
			justify-content: space-around;
			margin: 0px auto;border-radius: 20rpx;background-color: rgba(248, 247, 247, 0.5);">
			<view style="display: flex;" v-if="isAuth">
				<view style="width: 150rpx;height: 150rpx;
								background-color: #f0f0f0;
								border-radius: 50%;
							text-align: center;
							align-items: center;
							justify-content: center;
								display: flex;">
					<view style="width: 140rpx;height: 140rpx;;background-color:#FFFFFF;border-radius: 50%;">
						<image :src="userInfo.avatarUrl" style="width: 140rpx;height: 140rpx;border-radius: 50%;">
						</image>
					</view>
				</view>
			</view>
			<view v-if="isAuth" style="letter-spacing: 0.1rem;font-size: 1.2rem;">
				{{userInfo.nickName}}
			</view>
			<view v-else style="">
				<button class="login" style="" @click="authentication()">授权登录></button>
			</view>
		</view>
		<view style="display: flex;justify-content: space-between;width: 95vw;margin: auto;padding: 20rpx 0rpx;">
			<view
				style="height: 150rpx;width: 25vw;padding: 20rpx 30rpx;background-color: #f0f0f0;border-radius: 20rpx;display: flex;flex-direction: column;justify-content: space-around;text-align: center;font-family: '黑体';">
				<view style="font-size: 1rem;color: #242424;">{{userInfo.uploadNumber}}</view>
				<view style="font-size: 0.9rem;color: #4e4e4e;">上传总数</view>
			</view>
			<view
				style="height: 150rpx;width: 25vw;padding: 20rpx 30rpx;background-color: #f0f0f0;border-radius: 20rpx;display: flex;flex-direction: column;justify-content: space-around;text-align: center;font-family: '黑体';">
				<view style="font-size: 1rem;color: #242424;">{{userInfo.downloadNumber}}</view>
				<view style="font-size: 0.9rem;color: #4e4e4e;">下载总数</view>
			</view>
			<view
				style="height: 150rpx;width: 25vw;padding: 20rpx 30rpx;background-color: #f0f0f0;border-radius: 20rpx;display: flex;flex-direction: column;justify-content: space-around;text-align: center;font-family: '黑体';">
				<view style="font-size: 1rem;color: #242424;">{{userInfo.days}}</view>
				<view style="font-size: 0.9rem;color: #4e4e4e;">在线时间</view>
			</view>
		</view>
		<view
			style="display: flex;flex-direction: column;width: 90vw;margin: auto;justify-content: space-around;height: 480rpx;">
			<view @click="updateUserInfo()"
				style="width: 90vw;padding: 26rpx 0rpx;background-color: #f0f0f0;display: flex;justify-content: space-between;border-radius: 15rpx;">
				<view style="display: flex;width: 60%;align-items: center;padding-left: 40rpx;">
					<img src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/updateIcon.png" alt=""
						style="width: 40rpx;height: 40rpx;">
					<view style="color: #4e4e4e;font-family: '楷体';font-size: 1rem;padding-left: 40rpx;">资料修改</view>
				</view>
				<view style="padding-right: 20rpx;display: flex;align-items: center;">
					<img src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/right.png"
						style="width: 45rpx;height: 45rpx;" alt="">
				</view>
			</view>
			<view
				style="width: 90vw;padding: 26rpx 0rpx;background-color: #f0f0f0;display: flex;justify-content: space-between;border-radius: 15rpx;">
				<view style="display: flex;width: 60%;align-items: center;padding-left: 40rpx;">
					<img src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/feedIcon.png" alt=""
						style="width: 25px;height: 25px;transform: scale(1.2);">
					<button open-type="feedback" class="feedback" style=""> 问题反馈</button>
				</view>
				<view style="padding-right: 20rpx;display: flex;align-items: center;">
					<img src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/right.png"
						style="width: 45rpx;height: 45rpx;" alt="">
				</view>
			</view>
			<view
				style="width: 90vw;padding: 26rpx 0rpx;background-color: #f0f0f0;display: flex;justify-content: space-between;border-radius: 15rpx;">
				<view style="display: flex;width: 60%;align-items: center;padding-left: 40rpx;">
					<img src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/share.png" alt=""
						style="width: 25px;height: 25px;transform: scale(0.8);">
					<button open-type="share" class="feedback" style="">分享程序</button>
				</view>
				<view style="padding-right: 20rpx;display: flex;align-items: center;">
					<img src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/right.png"
						style="width: 45rpx;height: 45rpx;" alt="">
				</view>
			</view>
			<view @click="about()"
				style="width: 90vw;padding: 26rpx 0rpx;background-color: #f0f0f0;display: flex;justify-content: space-between;border-radius: 15rpx;">
				<view style="display: flex;width: 60%;align-items: center;padding-left: 40rpx;">
					<img src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/aboutIcon.png" alt=""
						style="width: 45rpx;height: 45rpx;">
					<view style="color: #4e4e4e;font-family: '楷体';font-size: 1rem;padding-left: 40rpx;">关于软件</view>
				</view>
				<view style="padding-right: 20rpx;display: flex;align-items: center;">
					<img src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/right.png"
						style="width: 45rpx;height: 45rpx;" alt="">
				</view>
			</view>

		</view>

	</view>
</template>

<script>
	import requestUrl from "../../api/request.js"
	export default {
		data() {
			return {
				isAuth: false,
				userInfo: {}
			}
		},
		onLoad() {
			this.getUserInfo();
		},
		methods: {
			authentication() {
				uni.navigateTo({
					url: '/pages/auth/auth'
				})
			},
			getUserInfo() {
				uni.request({
					url: requestUrl.prod.BASE_URL + "/user/getUserInfo",
					method: "GET",
					header: {
						"Authorization": wx.getStorageSync("token")
					},
					success: (res) => {
						if (res.data.code == 200) {
							this.isAuth = true;
							this.userInfo = res.data.data.userInfo
						} else {
							this.isAuth = false;
						}
					}
				})
			},
			updateUserInfo() {
				if (wx.getStorageSync('isAuth') === '') {
					uni.showToast({
						title: '请先登录',
						icon: 'error',
						duration: 2000
					});
				} else {
					uni.navigateTo({
						url: "/pages/updateUserInfo/updateUserInfo"
					})
				}
			},
			about() {
				uni.navigateTo({
					url: "/pages/about/about"
				})
			},
			share() {
				wx.showShareMenu({
					withShareTicket: true,
					menus: ['shareAppMessage', 'shareTimeline']
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	page {
		height: 100%;
		width: 100%;
	}

	.max {
		height: 100%;
		background-color: #FFFFFF;
		/* 	background: linear-gradient(to bottom, rgba(248, 234, 200, 0.8), #FFFFFF); */
	}

	.login {
		/* 	background-color: #f6d8e3; */

		background-color: #f0f0f0;
		letter-spacing: 0.1rem;
		color: #5b5b5b;
	}

	.feedback {
		outline: none !important;
		border: none !important;
		border-radius: 0;
		background-color: white;
		margin: 0rpx;
		color: #4e4e4e;
		font-family: '楷体';
		font-size: 1rem;
		padding-left: 40rpx;
		background-color: #f0f0f0;
		padding: 0px 0px 0px 20px !important;
		line-height: normal;
	}

	button:after {
		border: none !important;
	}
</style>
