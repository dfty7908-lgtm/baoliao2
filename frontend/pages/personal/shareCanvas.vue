<template>
	<view>
		<l-painter ref="cheshi" @progress='schedule' v-if="showValue==1" class="share_class">
			<l-painter-view css="height:900rpx; width:600rpx;border-radius:20rpx;background-color:#ff945e;">
				<l-painter-image src="../../static/image/personal/用户邀请.png" css="width:600rpx; height:900rpx;" />
				<!-- <l-painter-qrcode :text="user.userQrCode"
					css="width:145rpx;height:145rpx;background:white;position:absolute;top:690rpx;right:70rpx;" /> -->
					<image :src="user.userQrCode" style="width:145rpx;height:145rpx;background:white;position:absolute;top:690rpx;right:70rpx;"></image>
			</l-painter-view>
		</l-painter>
		<l-painter ref="cheshi" @progress='schedule' v-if="showValue==2" class="share_class">
			<l-painter-view css="height:900rpx; width:600rpx;border-radius:20rpx;background-color:#ff945e;">
				<l-painter-image src="../../static/image/personal/店主邀请.png" css="width:600rpx; height:900rpx;" />
				<!-- <l-painter-qrcode text=""
					css="width:145rpx;height:145rpx;background:white;position:absolute;top:680rpx;right:220rpx;" /> -->
					<image :src="user.qrCode" style="width:145rpx;height:145rpx;background:white;position:absolute;top:680rpx;right:220rpx;"></image>
			</l-painter-view>
		</l-painter>
		<view class="type_box">
			<view class="buttom_style" :style="showValue==1?'background-color: #3999FE;':''" @click="clickButton(1)">
				邀请用户</view>
			<view class="buttom_style" :style="showValue==2?'background-color: #3999FE;':''" @click="clickButton(2)">
				邀请店主</view>
		</view>
		<view class="popupShareCard">
			<view class="cancelButtom" @click="back">
				取消
			</view>
		</view>
	</view>
</template>
<script>
	import {
		getUser
	} from '@/api/user';
	import painter from '@/components/lime-painter/components/l-painter/l-painter.vue';
	import painter_qrcode from '@/components/lime-painter/components/l-painter-qrcode/l-painter-qrcode.vue';
	import painter_view from '@/components/lime-painter/components/l-painter-view/l-painter-view.vue';
	import painter_image from '@/components/lime-painter/components/l-painter-image/l-painter-image';
	export default {
		components: {
			"l-painter": painter,
			"l-painter-qrcode": painter_qrcode,
			"l-painter-view": painter_view,
			"l-painter-image": painter_image
		},
		data() {
			return {
				showValue: 1,
				user: {}
			}
		},
		onLoad() {
			this.init()
		},
		methods: {
			schedule(e) {
				if (e == 1) {
					uni.hideLoading()
				}
			},
			clickButton(type) {
				this.showValue = type
			},
			async init() {
				uni.showLoading({
					title: '生成中~'
				})
				let user = await getUser();
				this.user = user;
			},
			back() {
				uni.navigateBack()
			},
		}
	}
</script>

<style scoped>
	.buttom_style {
		width: 250rpx;
		height: 80rpx;
		border-radius: 15rpx;
		text-align: center;
		line-height: 80rpx;
		color: white;
		font-weight: bold;
		background-color: #d6d6d6;
	}

	.type_box {
		width: 100%;
		display: flex;
		justify-content: space-around;
		margin-top: var(--status-bar-height);
		padding: 20rpx 0;
	}

	.share_class {
		position: fixed;
		top: 50%;
		left: 50%;
		margin-top: -550rpx;
		margin-left: -300rpx;
		width: 600rpx;
		border-radius: 20rpx;
	}

	.buttonStyle {
		width: 300rpx;
		height: 60rpx;
		text-align: center;
		line-height: 60rpx;
		font-weight: bold;
		border-radius: 20rpx;
		transition: all 0.2s;
	}

	.buttonStyle_true {
		width: 300rpx;
		height: 60rpx;
		text-align: center;
		line-height: 60rpx;
		background-color: #FDC830;
		color: white;
		font-weight: bold;
		border-radius: 20rpx;
		transition: all 0.2s;
	}

	.buttonBox {
		width: 600rpx;
		display: flex;
		justify-content: space-between;
		margin: auto;
		border-radius: 20rpx;
		box-shadow: -1px -1px 1px #a5a5a5, 1px 1px 1px #fff;
	}

	.popupShareCard {
		width: 100%;
		display: flex;
		flex-flow: column;
		justify-content: space-between;
		background-color: white;
		border-radius: 40rpx 40rpx 0rpx 0rpx;
		position: fixed;
		bottom: 0rpx;
	}

	.cancelButtom {
		width: 600rpx;
		height: 80rpx;
		background-color: #ebebeb;
		margin: 15rpx auto;
		border-radius: 20rpx;
		text-align: center;
		line-height: 80rpx;
	}

	.cancelButtom:active {
		transition: .2s;
		transform: scale(0.95);
	}


	.canvasStyle {
		width: 600rpx;
		height: 900rpx;
		border-radius: 20rpx;
		z-index: 9999;
		margin: 50rpx auto;
		background-color: white;
	}

	page {
		background-color: #ebebeb;
	}
</style>