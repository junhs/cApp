<template>
	<view style="margin-top: 10rpx;">
		<u-row justify="space-between">
			<u-row>
				<view>
					<text
						style="border-radius: 10rpx;padding: 8rpx 20rpx ;text-align: center;font-size: 14px;background-color: rgba(168,153,230,0.1);color: #85a3ff; "
						v-if="data &&data.category && data.category.length>0">{{data.category[0].name}}</text>
				</view>
				<block v-for="(tag,index) in data.tag" :key="index" v-if="data.tag">
					<view>
						<text
							style="border-radius: 10rpx;padding:8rpx 8rpx;text-align: center;font-size: 14px;">#{{tag.name}}</text>
					</view>
				</block>
			</u-row>
		</u-row>
		<view v-if="data.opt&&data.opt.files[0].link">
			<block v-for="(item,index) in data.opt.files" :key="index">
				<u-row justify="space-between" customStyle="margin-bottom:10rpx">
					<u-row customStyle="background: #f7f7f7;border-radius: 10rpx;height:60rpx;flex:1"
						@click="openUrl(item.link,'提取码',item.password)">
						<u-icon name="download" size="18"></u-icon>
						<text class="u-line-1">{{item.link}}</text>
					</u-row>
					<view>
						<u-button plain
							customStyle="height:60rpx;width:auto;font-size:30rpx;border-radius:10rpx;margin-left:20rpx"
							color="#85a3ff" @click="copy(item.unzipPass,'解压码')">解压码</u-button>
					</view>
				</u-row>
			</block>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'articleFooter',
		props: {
			data: {
				type: Object,
				default: null,

			}
		},
		data() {
			return {

			}
		},
		created() {

		},
		methods: {
			openUrl(url, name, data) {
				setTimeout(() => {
					plus.runtime.openURL(url)
				}, 500)
				if (data) {
					this.copy(data, name)
				}
			},
			copy(data, name) {
				uni.setClipboardData({
					data: data,
					showToast: false,
					success: (res) => {
						uni.$u.toast(name + '已复制')
					}
				})
			}
		}
	}
</script>

<style lang="scss">

</style>