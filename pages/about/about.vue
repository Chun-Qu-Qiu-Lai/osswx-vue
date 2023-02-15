<template>
	<view>
		<cu-custom bgColor="#ffffff" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">关于软件</block>
		</cu-custom>

	</view>
</template>

<script>
</script>

<style lang="scss" scoped>
	page {
		height: 100%;
	}

	view {
		height: 100vh;
		background: url('https://oss-zscyun.oss-cn-beijing.aliyuncs.com/oss-icon/about.png') no-repeat;
		background-size: 100% 100%;
		background-attachment:fixed;
	}
</style>
