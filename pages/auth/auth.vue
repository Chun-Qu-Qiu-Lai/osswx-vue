<template>
	<view style="">
		<cu-custom bgColor="bg-gray" :isBack="true">
			<block slot="backText">取消</block>
			<block slot="content">登录</block>
		</cu-custom>

		<button class="avatar-wrapper" open-type="chooseAvatar" @chooseavatar="onChooseAvatar">
			<image class="avatar" :src="avatarUrl"></image>
		</button>
		<view class="cu-form-group margin-top">
			<view class="title">昵称</view>
			<input type="nickname" placeholder="请输入昵称" v-model="nickName" id="nickname-input"
				@blur="getNickName"></input>
		</view>
		<view class="cu-form-group margin-top">
			<view class="title">账号</view>
			<input type="username" placeholder="请输入账号" v-model="username"></input>
		</view>
		<view class="cu-form-group margin-top">
			<view class="title">密码</view>
			<input type="password" placeholder="请输入密码" v-model="password"></input>
		</view>
		<!-- <input type="nickname" placeholder="请输入昵称" v-model="nickName" id="nickname-input" @blur="getNickName" /> -->
		<button class="cu-btn bg-blue lg shadow"
			style="width: 50%;margin:30% auto;align-items: center;text-align: center;display: flex;"
			@click="authorization()">确认</button>
	</view>
</template>

<script>
	import requestUrl from "../../api/request.js"
	export default {
		data() {
			return {
				nickName: null,
				username: '',
				password: '',
				avatarUrl: 'https://mmbiz.qpic.cn/mmbiz/icTdbqWNOwNRna42FI242Lcia07jQodd2FJGIYQfG0LAJGFxM4FbnQP6yfMxBgJ0F3YRqJCJ1aPAK2dQagdusBZg/0',
				code: null
			}
		},
		onLoad() {},
		methods: {
			onChooseAvatar(e) {
				this.avatarUrl = e.detail.avatarUrl
			},
			authorization() {
				if (this.nickName === '' || this.nickName === null || this.nickName == undefined) {
					uni.showToast({
						title: '昵称不为空',
						icon: 'error',
						duration: 1000
					});
					return
				}
				if (this.nickName.length > 7) {
					uni.showToast({
						title: '昵称小于七位',
						icon: 'error',
						duration: 1000
					});
					return
				}
				if (this.username.length === '') {
					uni.showToast({
						title: '用户名不为空',
						icon: 'error',
						duration: 1000
					});
					return
				}
				if (this.username.length > 15) {
					uni.showToast({
						title: '用户名最大10位',
						icon: 'error',
						duration: 1000
					});
					return
				}
				if (this.password.length === '') {
					uni.showToast({
						title: '密码不为空',
						icon: 'error',
						duration: 1000
					});
					return
				}
				if (this.password.length > 15) {
					uni.showToast({
						title: '密码最大10位',
						icon: 'error',
						duration: 1000
					});
					return
				}
				if (this.avatarUrl ===
					'https://mmbiz.qpic.cn/mmbiz/icTdbqWNOwNRna42FI242Lcia07jQodd2FJGIYQfG0LAJGFxM4FbnQP6yfMxBgJ0F3YRqJCJ1aPAK2dQagdusBZg/0'
				) {
					uni.showToast({
						title: '头像不为空',
						icon: 'error',
						duration: 1000
					});
					return
				}
				uni.login({
					provider: "weixin",
					success: (res) => {
						this.code = res.code
						uni.request({
							url: requestUrl.prod.BASE_URL + "/user/applet/authorization",
							method: "POST",
							data: {
								code: this.code,
								avatarUrl: this.avatarUrl,
								nickName: this.nickName,
								username: this.username,
								password: this.password
							},
							header: {
								"content-type": "application/json"
							},
							success: (res) => {
								if (res.data.code == 200) {
									wx.setStorageSync("avatarUrl", this.avatarUrl)
									wx.setStorageSync("nickName", this.nickName)
									wx.setStorageSync("isAuth", true)
									uni.reLaunch({
										url: '/pages/my/my'
									})
									uni.showToast({
										title: "登录成功",
										icon: 'error',
										duration: 1000
									});
								} else {
									uni.showToast({
										title: res.data.msg,
										icon: 'error',
										duration: 1000
									});
								}
							}
						})
					}
				})
			},
			getNickName() {
				uni.createSelectorQuery().in(this)
					.select("#nickname-input")
					.fields({
						properties: ["value"],
					})
					.exec((res) => {
						const nickName = res?. [0]?.value
						this.nickName = nickName
					});
			}
		}
	}
</script>

<style lang="scss" scoped>
	.avatar-wrapper {
		padding: 0;
		width: 56px !important;
		border-radius: 8px;
		margin-top: 40px;
		margin-bottom: 40px;
	}

	.avatar {
		display: block;
		width: 56px;
		height: 56px;
	}

	.container {
		display: flex;
	}
</style>
