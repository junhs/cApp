<template>
	<view>
		<z-paging ref="paging" @query="getMessages" v-model="messages" use-chat-record-mode auto-hide-keyboard-when-chat
			use-page-scroll v-if="roomId">
			<template #top>
				<u-navbar placeholder autoBack :title="nickname"></u-navbar>
			</template>
			<view style="margin: 30rpx;">
				<block v-for="(item,index) in messages" :key="index">
					<u-row v-if="item.uid != userInfo.uid" align="top" customStyle="margin-bottom: 20rpx;">
						<u-avatar :src="item.userJson.avatar"></u-avatar>
						<view
							style="background: #85a3ff32;padding:10rpx;margin-left: 10rpx;border-radius: 20rpx;margin-top: 10rpx;">
							<uv-parse :tagStyle="{img:'border-radius:10px'}" :content="item.text"
								style="word-wrap: normal;flex-wrap: wrap; word-break: break-all;"></uv-parse>
						</view>
					</u-row>
					<u-row v-if="item.uid == userInfo.uid" justify="end" align="top"
						customStyle="margin-bottom: 20rpx;">
						<view
							style="background: #85a3ff32;padding:10rpx;margin-right: 10rpx;border-radius: 20rpx;margin-top: 10rpx;">
							<uv-parse :tagStyle="{img:'border-radius:10px'}" :content="item.text"
								style="word-wrap: normal;flex-wrap: wrap; word-break: break-all;"></uv-parse>
						</view>
						<u-avatar :src="userInfo.avatar" />
					</u-row>
				</block>
			</view>
			<template #bottom>
				<view style="padding: 30rpx;background: #fff;">
					<u-row align="bottom">
						<editor id="editor" @ready="onEditorReady"
							style="background: #85a3ff32;height: auto;min-height: unset;max-height: 100px;border-radius: 20rpx;padding: 8rpx 16rpx;">
						</editor>
						<u-button color="#85a3ff" style="width: 140rpx;height: 60rpx;margin-left: 20rpx;" shape="circle"
							@click="sendMessage()">发送</u-button>
					</u-row>
				</view>
			</template>
		</z-paging>
	</view>
</template>

<script>
	import {
		mapState
	} from 'vuex';
	export default {
		data() {
			return {
				nickname: null,
				id: 0,
				roomId: 0,
				messages: [],
				editorCtx: null,
			}
		},
		onPageScroll(e) {
			if(e.scrollTop<=0){
				this.$refs.paging.doChatRecordLoadMore()
			}
		},
		onLoad(params) {
			this.nickname = params.nickname
			this.id = params.id
			this.getData(params.id)
			console.log(params)
		},
		computed: {
			...mapState(['userInfo'])
		},
		methods: {
			getData(id) {
				this.$http.post('/typechoChat/getPrivateChat', {
					touid: this.id
				}).then(res => {
					console.log(res)
					if (res.data.code) {
						this.roomId = res.data.data
					}
				})
			},
			getMessages(page) {
				this.$http.post('/typechoChat/msgList', {
					page,
					limit: 30,
					chatid: this.roomId
				}).then(res => {
					console.log(res)
					if (res.data.code) {
						this.$refs.paging.complete(res.data.data)
					}
				})
			},
			sendMessage() {
				this.editorCtx.getContents({
					success: (res) => {
						this.$http.post('/typechoChat/sendMsg', {
							chatid: this.roomId,
							type: 0,
							msg: res.text
						}).then(ress => {
							if (ress.data.code) {

								// 将消息添加到列表
								this.$refs.paging.addChatRecordData({
									uid: this.userInfo.uid,
									userJson: {
										uid: this.userInfo.uid
									},
									text: res.text
								})
								// 清空编辑器消息
								this.editorCtx.clear()
							}
						})
					}
				})
			},
			shouldDisplayAvatar(index) {
				// 如果是第一条消息，或者当前消息用户与上一条消息用户不同，则显示当前消息用户的头像
				if (index === 0 || this.messages[index].userJson.uid !== this.messages[index - 1].userJson.uid) {
					return this.messages[index].userJson.avatar;
				}

				// 否则不显示头像
				return 'hidden';
			},
			onEditorReady() {
				// #ifdef MP-BAIDU
				this.editorCtx = requireDynamicLib('editorLib').createEditorContext('editor');
				// #endif

				// #ifdef APP-PLUS || H5 ||MP-WEIXIN
				uni.createSelectorQuery().select('#editor').context((res) => {
					this.editorCtx = res.context
				}).exec()
				// #endif
			},
		}
	}
</script>

<style lang="scss">
	.ql-container ::v-deep .ql-blank::before {
		min-height: 60rpx;
		height: 60rpx;
		font-style: normal;
	}
</style>