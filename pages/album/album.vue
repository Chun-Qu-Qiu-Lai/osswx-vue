<template>
	<view class="max">
		<cu-custom bgColor="bg-gray" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">上传</block>
		</cu-custom>
		<view style="padding: 20rpx 2rpx;">
			<view class="example-body" style="background-color: #a9c666;padding: 20rpx 0rpx">
				<uni-file-picker ref="files" :autoUpload="false" v-model="imageValue" fileMediatype="image" limit="3"
					@select="select" @delete="deleteTempFile">
				</uni-file-picker>
			</view>
		</view>
		<button @click="goText()"
			style="background-color: #a9c666;color: #fff;padding: 5rpx 20rpx;width: 40vw;letter-spacing: 0.1rem;">上传图片</button>

		<view style="padding: 30rpx 0rpx;">
			<view style="width: 95%;height: 4rpx;background-color: coral;margin:0rpx auto;"></view>
		</view>

		<view style="width: 98%;margin: auto;">
			<view v-for="(item,index) in files" :key="index" style="border: #b2c64a 2rpx solid;border-radius: 5rpx;width: 230rpx;
				height: 230rpx;
				display: inline-block;
				margin-left: 10rpx;" @tap="showChoseModal" data-target="Modal" :data-fileUrl="item.fileUrl"
				:data-filePath="item.filePath" :data-fileId="item.fileId">
				<image :src="item.fileUrl" style="width: 230rpx;height: 230rpx;"></image>
			</view>
		</view>
		<!--  -->
		<view class="cu-modal" :class="modalName=='Modal'?'show':''">
			<view class="cu-dialog">
				<view class="cu-bar bg-white justify-end">
					<view class="content">请选择</view>
					<view class="action" @tap="hideModal">
						<text class="cuIcon-close text-red"></text>
					</view>
				</view>
				<view style="display: flex;flex-direction: column;
					align-items: center;justify-content: space-around;
					padding: 20rpx 40rpx;">
					<button class="cu-btn bg-green" style="width: 50%;" @click="fileDownload()">下载图片</button>
					<button class="cu-btn bg-olive" style="width: 50%;margin-top: 10rpx;"
						@click="previewFile()">全屏预览</button>
					<button class="cu-btn bg-olive" style="width: 50%;margin-top: 10rpx;"
						@click="shareFile()">分享图片</button>
					<button class="cu-btn bg-red" style="width: 50%;margin-top: 10rpx;" @tap="showDeleteModal"
						data-target="DialogModal1">删除图片</button>
				</view>
			</view>
		</view>
		<!--  -->
		<view class="cu-modal" :class="modalName=='DialogModal1'?'show':''">
			<view class="cu-dialog">
				<view class="cu-bar bg-white justify-end">
					<view class="content text-red">是否确认删除该图片？</view>
				</view>
				<view class="cu-bar bg-white justify-end">
					<view class="action">
						<button class="cu-btn line-green text-green" @tap="cancelDelete">取消</button>
						<button class="cu-btn bg-green margin-left" @tap="confirmDelete">确定</button>
					</view>
				</view>
			</view>
		</view>
		<!--  -->
	</view>
</template>

<script>
	import requestUrl from "../../api/request.js"
	import cuCustom from "../../colorui/components/cu-custom.vue"
	export default {
		data() {
			return {
				catalogueId: null,
				//没用
				imageValue: [],
				//文件信息
				imageFiles: [],
				//签名信息
				formData: {
					key: '',
					policy: '',
					OSSAccessKeyId: 'LTAI5tNaT4y5Ta2YVh6YpU82',
					success_action_status: '200',
					callback: '',
					signature: '',
				},
				host: '',
				files: [],
				modalName: null,
				downloadUrl: '',
				filePath: '',
				fileId: ''
			}
		},
		onLoad(options) {
			this.catalogueId = options.catalogueId
			this.getFiles()
		},
		methods: {
			async goText() {
				var imageFilesLength = this.imageFiles.length
				for (let i = 0; i < imageFilesLength; i++) {
					const res = await this.getSignature()
					if (res.data.code === 200) {
						this.formData.key = res.data.data.dir,
							this.formData.policy = res.data.data.policy,
							this.formData.OSSAccessKeyId = res.data.data.accessKeyId,
							this.formData.success_action_status = '200',
							this.formData.callback = res.data.data.callback,
							this.formData.signature = res.data.data.signature
						this.host = res.data.data.host
					} else {
						uni.showToast({
							title: '获取签名失败',
							icon: 'error',
							duration: 1000
						});
					}
					uni.uploadFile({
						url: this.host,
						filePath: this.imageFiles[i].path,
						name: 'file',
						formData: this.formData,
						success: (res) => {
							if (res.statusCode === 200) {
								uni.showToast({
									title: '上传成功',
									icon: 'success',
									duration: 1000
								});
								this.getFiles()
							} else {
								uni.showToast({
									title: '上传失败',
									icon: 'error',
									duration: 1000
								});
							}
						}
					});
				}
				this.imageFiles = []
				this.$refs.files.clearFiles()
			},
			async getSignature() {
				return new Promise((resolve, reject) => {
					uni.request({
						url: requestUrl.prod.BASE_URL + "/oss/policy",
						method: "POST",
						data: {
							catalogueId: this.catalogueId
						},
						header: {
							"Authorization": wx.getStorageSync("token"),
							'content-type': 'application/x-www-form-urlencoded'
						},
						success: (res) => {
							resolve(res)
						}
					})
				})
			},
			deleteTempFile(e) {
				for (var i = 0; i < this.imageFiles.length; i++) {
					if (this.imageFiles[i].path === e.tempFilePath) {
						this.imageFiles.splice(i, 1)
					}
				}
			},
			shareFile() {
				wx.downloadFile({
					url: this.downloadUrl,
					success: (res) => {
						wx.showShareImageMenu({
							path: res.tempFilePath
						})
						this.hideModal()
					}
				})

			},
			select(e) {
				for (var i = 0; i < e.tempFiles.length; i++) {
					this.imageFiles.push({
						'name': e.tempFiles[i].name,
						'path': e.tempFiles[i].path,
						'size': e.tempFiles[i].size
					})
				}
			},
			getFiles() {
				uni.request({
					url: requestUrl.prod.BASE_URL + "/oss/applet/getFiles",
					method: "POST",
					data: {
						catalogueId: this.catalogueId
					},
					header: {
						"Authorization": wx.getStorageSync("token"),
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: (res) => {
						if (res.data.code == 200) {
							this.files = res.data.data;
						}
					}
				})
			},
			showChoseModal(e) {
				this.modalName = e.currentTarget.dataset.target
				this.downloadUrl = e.currentTarget.dataset.fileurl
				this.filePath = e.currentTarget.dataset.filepath
				this.fileId = e.currentTarget.dataset.fileid
			},
			showDeleteModal(e) {
				this.modalName = e.currentTarget.dataset.target
			},
			hideModal(e) {
				this.modalName = null
			},
			fileDownload() {
				wx.downloadFile({
					url: this.downloadUrl,
					success: (res) => {
						if (res.statusCode === 200) {
							var temp = res.tempFilePath
							wx.saveImageToPhotosAlbum({
								filePath: temp,
								success: (res) => {
									if (res.errMsg === "saveImageToPhotosAlbum:ok") {
										this.hideModal()
										uni.request({
											url: requestUrl.prod.BASE_URL +
												"/oss/download",
											method: "POST",
											data: {
												fileId: this.fileId,
											},
											header: {
												"Authorization": wx.getStorageSync(
													"token"),
												'content-type': 'application/x-www-form-urlencoded'
											},
											success: (res) => {
												if (res.data.code === 200) {
													uni.showToast({
														title: "下载成功",
														icon: 'success',
														duration: 1000
													});
												} else {
													uni.showToast({
														title: "下载失败",
														icon: 'error',
														duration: 1000
													});
												}
											}
										})
									}
								}
							})
						}
					}
				});
			},
			confirmDelete() {
				uni.request({
					url: requestUrl.prod.BASE_URL + "/oss/removeFile",
					method: "POST",
					data: {
						filePath: this.filePath,
						fileId: this.fileId
					},
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						"Authorization": wx.getStorageSync("token")
					},
					success: (res) => {
						if (res.data.code == 200) {
							this.modalName = null
							uni.showToast({
								title: "删除成功",
								icon: 'success',
								duration: 1000
							});
							this.getFiles()
						} else {
							uni.showToast({
								title: "删除失败",
								icon: 'error',
								duration: 1000
							});
						}
					}
				})
			},
			cancelDelete() {
				this.modalName = null
			},
			previewFile() {
				wx.previewImage({
					current: this.downloadUrl,
					urls: [this.downloadUrl],
					success: (res) => {
						this.modalName = null
					}
				})
			},
		}
	}
</script>

<style lang="scss" scoped>
	page {
		width: 100%;
		height: 100%;
		background-color: #f4f4f4;
	}

	.max {
		height: 100%;
	}
</style>
