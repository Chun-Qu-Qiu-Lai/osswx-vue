<template>
	<view class="max" style="padding-top: 20%;">
		<image src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/logo.png" mode="widthFix"
			style="width: 100%;height: 0rpx;display: flex;align-items: center;justify-content: center;"></image>

		<swiper class="card-swiper" :class="dotStyle?'square-dot':'round-dot'" :indicator-dots="true" :circular="true"
			:autoplay="true" interval="5000" duration="500" @change="cardSwiper" indicator-color="#8799a3"
			indicator-active-color="#0081ff">
			<swiper-item v-for="(item,index) in swiperList" :key="index" :class="cardCur==index?'cur':''">
				<view class="swiper-item">
					<image :src="item.url" mode="aspectFill" v-if="item.type=='image'"></image>
					<video :src="item.url" autoplay loop muted :show-play-btn="false" :controls="false"
						objectFit="cover" v-if="item.type=='video'"></video>
				</view>
			</swiper-item>
		</swiper>

		<view style="display: flex;width: 90%;justify-content: space-around;margin:20rpx auto;">
			<view @tap="showChoseModal" data-target="Modal" class="btn"
				style="border: 8rpx #d65083 solid;display: flex;justify-content: space-between;padding-right: 10rpx;align-items: center;">

				<image
					style="width: 80rpx;height: 80rpx;display: flex;align-items: center;margin: auto;"
					src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/cPic.png"></image>
				<view style="	letter-spacing: 0.1rem;
		font-weight: bolder;
		text-align: center;
		line-height: 80rpx;
		text-shadow: 2px 2px 4px #ffffff;height: 80rpx;">创建相册</view>

			</view>
			<view class="btn" @click="toAlbum()" 	style="border: 8rpx #d65083 solid;display: flex;justify-content: space-between;padding-right: 10rpx;align-items: center;">
			<view style="border: 8rpx #515151 solid;border-radius: 50%;transform: scale(0.8);">
				<image
					style="transform: scale(0.6);width: 70rpx;
					height: 70rpx;display: flex;
					align-items: center;margin: auto;
					"
					src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/sousuo.png"></image>
			</view>
				
					<view style="	letter-spacing: 0.1rem;
			font-weight: bolder;
			text-align: center;
			line-height: 80rpx;
			text-shadow: 2px 2px 4px #ffffff;height: 80rpx;">查看相册</view>
			</view>
		</view>



		<view
			style="display: grid;grid-template-columns: repeat(3,200rpx);align-items: center;grid-gap: 40rpx 30rpx;padding: 20rpx 30rpx;">
			<!-- v-for="(item,index) in catalogues" :key="index" -->
			<view v-for="(item,index) in catalogues" :key="index" @click="setAlbum(item.catalogueId)" class="catalogue"
				style="height:190rpx;border-radius: 20rpx;">
				<view style="border-radius: 20rpx;width: 200rpx;background-color: aqua;
					height: 150rpx;
					background-color: rgba(255,255,248,0.8);padding: 10rpx;margin-bottom: 10rpx;box-shadow: 0 0 20rpx -1rpx ;">
					<image style="width: 270rpx;height: 130rpx;"
						src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/pic.png"></image>
				</view>
				<view style="text-align: center;font-size: 16px;letter-spacing: 0.1rem;">
					{{item.name}}
				</view>
			</view>
		</view>
		<!--  -->
		<view class="cu-modal" :class="modalName=='Modal'?'show':''">
			<view class="cu-dialog">
				<view class="cu-bar bg-white justify-end">
					<view class="action" @tap="hideModal">
						<text class="cuIcon-close text-red"></text>
					</view>
				</view>
				<view style="display: flex;flex-direction: column;
					align-items: center;justify-content: space-around;
					padding: 20rpx 20rpx;">
					<input v-model="catalogueName" placeholder="请输入相册名称"
						style="border-bottom: #ff9460  2rpx solid;padding: 5rpx 0rpx;height: 80rpx;background-color: #f1f1f1;font-size: 18px;margin-bottom: 20rpx;" />
					<button class="cu-btn margin-sm basis-sm shadow"
						:class="['bg-' + item.color,animation==item.name?'animation-' +item.name :'']" @tap="Toggle"
						:data-class="item.name" v-for="(item,index) in list" :key="index"
						style="background-color: #f7637b;width: 50%;padding: 18rpx 0rpx;"
						@click="createCatalogue()">确认</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import requestUrl from "../../api/request.js"
	import cuCustom from "../../colorui/components/cu-custom.vue"
	export default {
		components: {
			cuCustom
		},
		data() {
			return {
				/*  */
				cardCur: 0,
				swiperList: [{
					id: 0,
					type: 'image',
					url: 'https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/rotationOne.png',
				}, {
					id: 1,
					type: 'image',
					url: 'https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/rotationTwo.png',
				}, {
					id: 2,
					type: 'image',
					url: 'https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/rotationThree.png',
				}],
				dotStyle: false,
				towerStart: 0,
				direction: '',
				catalogueName: '',
				catalogues: '',
				modalName: null,
				catalogueId: null,
				animation: '',
				list: [{
					name: 'scale-up',
					color: 'orange'
				}]
			}
		},
		onLoad() {
			this.login()
			this.TowerSwiper('swiperList')
			this.getCatalogues()
		},
		methods: {
			Toggle(e) {
				var anmiaton = e.currentTarget.dataset.class;
				this.animation = anmiaton;
				setTimeout(() => {
					this.animation = '';
				}, 1000)
			},
			ToggleDelay() {
				this.toggleDelay = true;
				setTimeout(() => {
					this.toggleDelay = false
				}, 1000)
			},
			login() {
				uni.login({
					provider: 'weixin',
					success: (res) => {
						this.code = res.code
						uni.request({
							url: requestUrl.prod.BASE_URL + '/user/applet/login',
							method: 'POST',
							data: {
								code: this.code,
							},
							header: {
								'content-type': 'application/x-www-form-urlencoded'
							},
							success: (res) => {
								if (res.data.code == 200) {
									wx.setStorageSync('token', 'Bearer' + res.data.data.token)
									console.log(wx.getStorageSync("token"));
								}
							}
						})
					}
				})
			},

			hideModal(e) {
				this.modalName = null
				this.catalogueName = ''
			},

			DotStyle(e) {
				this.dotStyle = e.detail.value
			},
			showChoseModal(e) {
				this.modalName = e.currentTarget.dataset.target
				this.catalogueName = ''
			},
			// cardSwiper
			cardSwiper(e) {
				this.cardCur = e.detail.current
			},
			// towerSwiper
			// 初始化towerSwiper
			TowerSwiper(name) {
				let list = this[name];
				for (let i = 0; i < list.length; i++) {
					list[i].zIndex = parseInt(list.length / 2) + 1 - Math.abs(i - parseInt(list.length / 2))
					list[i].mLeft = i - parseInt(list.length / 2)
				}
				this.swiperList = list
			},

			// towerSwiper触摸开始
			TowerStart(e) {
				this.towerStart = e.touches[0].pageX
			},

			// towerSwiper计算方向
			TowerMove(e) {
				this.direction = e.touches[0].pageX - this.towerStart > 0 ? 'right' : 'left'
			},

			// towerSwiper计算滚动
			TowerEnd(e) {
				let direction = this.direction;
				let list = this.swiperList;
				if (direction == 'right') {
					let mLeft = list[0].mLeft;
					let zIndex = list[0].zIndex;
					for (let i = 1; i < this.swiperList.length; i++) {
						this.swiperList[i - 1].mLeft = this.swiperList[i].mLeft
						this.swiperList[i - 1].zIndex = this.swiperList[i].zIndex
					}
					this.swiperList[list.length - 1].mLeft = mLeft;
					this.swiperList[list.length - 1].zIndex = zIndex;
				} else {
					let mLeft = list[list.length - 1].mLeft;
					let zIndex = list[list.length - 1].zIndex;
					for (let i = this.swiperList.length - 1; i > 0; i--) {
						this.swiperList[i].mLeft = this.swiperList[i - 1].mLeft
						this.swiperList[i].zIndex = this.swiperList[i - 1].zIndex
					}
					this.swiperList[0].mLeft = mLeft;
					this.swiperList[0].zIndex = zIndex;
				}
				this.direction = ""
				this.swiperList = this.swiperList
			},
			createCatalogue() {
				if (this.catalogueName === '' || this.catalogueName === null || this.catalogueName === undefined) {
					uni.showToast({
						title: "名称为空",
						icon: 'error',
						duration: 1000
					});
					this.hideModal()
					return
				}

				if (this.catalogueName.length > 7) {
					uni.showToast({
						title: "名称过长",
						icon: 'error',
						duration: 1000
					});
					this.hideModal()
					return
				}
				uni.request({
					url: requestUrl.prod.BASE_URL + "/catalogue/addCatalogue",
					method: "POST",
					data: {
						catalogueName: this.catalogueName
					},
					header: {
						"Authorization": wx.getStorageSync("token"),
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: (res) => {
						if (res.data.code == 200) {
							uni.showToast({
								title: "创建成功",
								icon: 'success',
								duration: 1000
							});
							this.hideModal()
							this.getCatalogues()
						} else {
							this.hideModal()
							uni.showToast({
								title: res.data.msg,
								icon: 'error',
								duration: 1000
							});
						}
					}
				})

			},
			getCatalogues() {
				uni.request({
					url: requestUrl.prod.BASE_URL + "/catalogue/applet/getCatalogues",
					method: "GET",
					header: {
						"Authorization": wx.getStorageSync("token")
					},
					success: (res) => {
						if (res.data.code == 200) {
							this.catalogues = res.data.data
						}
					}
				})
			},
			setAlbum(catalogueId) {
				this.catalogueId = catalogueId
			},
			showDeleteModal(e) {
				this.modalName = e.currentTarget.dataset.target
			},
			toAlbum() {
				if (this.catalogueId === null) {

				} else {
					uni.navigateTo({
						url: "/pages/album/album?" + "catalogueId=" + this.catalogueId
					})
				}
			}
		}

	}
</script>

<style lang="scss">
	@import "../../colorui/animation.css";

	image[class*="gif-"] {
		border-radius: 6upx;
		display: block;
	}

	page {
		width: 100%;
		height: 100%;
		background-image: linear-gradient(125deg, #fcd0cf 0%, #fef3ef 100%);
	}

	.max {
		/* 	background-image: linear-gradient(125deg, #fcd0cf 0%, #fef3ef 100%);
		height: 100%; */
	}

	.tower-swiper .tower-item {
		transform: scale(calc(0.5 + var(--index) / 10));
		margin-left: calc(var(--left) * 100upx - 150upx);
		z-index: var(--index);
	}

	.catalogue:hover {
		background-color: #788bd8;
		color: #fef3ef;
		/* 		background-color: rgba(193, 193, 188, 0.5); */
	}

	.btn {
		width: 265rpx;
		height: 125rpx;
		background-color: rgba(252, 235, 240, 1);
		border-radius: 62.5rpx;
		font-size: 17px;

		box-shadow: 0 2px 10px -1rpx #8d8d8d;
	}
</style>

<!-- 
 <view @tap="showChoseModal" data-target="Modal" class="shadow shadow-lg"
 				style="width: 250rpx;height: 150rpx;background-color: rgba(252, 235, 240,1);border-bottom-right-radius: 20rpx;border-top-right-radius: 20rpx;border-left: 20rpx #ff9460 solid;">
 				<view
 					style="color: #3b3769;font-size: 17px;text-align: center;letter-spacing: 0.1rem;font-weight: bolder;margin-bottom: 10rpx;">
 					创建相册</view>
 				<image src="#515151"
 					style="width: 100rpx;height: 100rpx;display: flex;margin-left: 130rpx;background-color: #d65083;"></image>
 			</view>
 			<view @click="toAlbum()"
 				style="width: 250rpx;height: 150rpx;background-color: rgba(252, 235, 240, 1);border-bottom-right-radius: 20rpx;border-top-right-radius: 20rpx;border-left: 20rpx #d65083 solid;">
 				<view
 					style="color: #3b3769;font-size: 17px;text-align: center;letter-spacing: 0.1rem;font-weight: bolder;margin-bottom: 10rpx;">
 					查看相册</view>
 				<image src="https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/selectPic.png"
 					style="width: 150rpx;height: 78rpx;margin: auto;display: flex;"></image>
 			</view>-->
