<template>
	<view style="margin-top:20rpx;">
		<!-- 内容 overflow:unset防止抖动-->
		<!-- <u-parse>组件错误更换为uv-parse -->
		<u-swiper height="200" :list="data.images" v-if="data&&data.images&&data.type=='photo'" :autoplay="false"
			indicator-mode="dot" @click="preview(data.images,$event)" style="margin-bottom: 20rpx;"
			indicator></u-swiper>
		<uv-parse
			:tag-style="{img:'border-radius:20rpx',video:'border-radius:20rpx !improtant',uniVideo:'border-radius:20rpx !improtant'}"
			style="overflow: unset;white-space: normal;word-break: break-all" :show-img-menu="!isScroll"
			:content="data.text" lazyLoad selectable @ready="$emit('ready',true)" v-if="data"></uv-parse>
	</view>
</template>

<script>
	import {
		formatTime
	} from '@/common/common.js';
	export default {
		name: 'articleContent',
		props: {
			data: {
				type: Object,
				default: null,
			},
			autoPreview: {
				type: Boolean,
				default: false,
			}
		},
		watch: {
			autoPreview: {
				handler(status) {
					this.isScroll = status
					console.log(this.isScroll)
				},
				deep: true,
				immediate: true
			},
		},
		computed: {

		},

		data() {
			return {
				isScroll: false,
			}
		},
		created() {
			console.log(this.data)
		},
		onReady() {
			// 如果没有加载出来就强制发送事件取消加载
			// APP端报Not Found
			setTimeout(() => {
				this.$emit('ready', true)
			}, 1200)
		},
		methods: {
			formatTime,
			is_last(index) {
				if (index != 2 || index != 5 || index != 8) return '10rpx'
			},
			preview(urls, index) {
				uni.previewImage({
					urls: urls,
					current: index
				})
			}
		}
	}
</script>

<style>
</style>