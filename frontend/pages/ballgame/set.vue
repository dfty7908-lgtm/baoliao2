<template>
	<view>
		<!-- 提示 -->
		<view style="width: 100%; height: 50rpx;background-color: #fffacf ;">
			<text style="line-height: 50rpx;font-size: 24rpx;margin-left: 20rpx; color: #2f2f2f ;">
				彩种管理：开关彩种功能后，将对用户开启/关闭相应功能
			</text>
		</view>
		<!-- 玩法开启 -->
		<view class="topTitle">
			<text style="font-size: 24rpx;padding-left: 20rpx;">玩法开启</text>
		</view>
		<!-- 内容 -->
		<view v-for="(itme,index) in ballGameDataLine" :key="index">
			<view class="biaoqian" @click="open(itme,index)">
				<view style="width: 150rpx;height: 125rpx;display: flex;align-items: center;justify-content: center;">
					<image :src='itme.url' style="width: 80rpx;height: 80rpx;">
					</image>
				</view>
				<view style="display: flex;flex-direction: column;line-height: 50rpx;width: 500rpx;">
					<text style="font-size: 26rpx; color: #303030;">{{itme.name}}</text>
					<view style="color: #9e9e9e;font-size:22rpx;">
						<text style="font-size:22rpx;">最小投注金额</text>
						<text style="color: red;font-size:22rpx;">{{itme.minPrice}}</text>
						<text style="font-size:22rpx;">元,停售前{{itme.advance}}分钟截止投注</text>
					</view>
				</view>
				<view style="height: 125rpx;line-height: 125rpx;margin-left: 45rpx;">
					<uni-icons type="right" color="#9e9e9e"></uni-icons>
				</view>
			</view>
		</view>

		<!-- 玩法关闭 -->
		<view style="width: 100%;height: 50rpx;background-color: #eeeeee;line-height: 50rpx;padding-bottom: 20upx;">
			<view class="topTitle">
				<text style="font-size: 24rpx;padding-left: 20rpx;">玩法关闭</text>
			</view>
			<view v-for="(d,index) in ballGameDataUnLine" :key="index">
				<view class="biaoqian" @click="open(d,index)">
					<view
						style="width: 150rpx;height: 125rpx;display: flex;align-items: center;justify-content: center;">
						<image :src='d.url' style="width: 80rpx;height: 80rpx;">
						</image>
					</view>
					<view style="display: flex;flex-direction: column;line-height: 50rpx;width: 500rpx;">
						<text style="font-size: 26rpx; color: #303030;">{{d.name}}</text>
						<view style="color: #9e9e9e ; font-size:22rpx;">
							<text>最小投注金额</text>
							<text style="color: red;">{{d.minPrice}}</text>
							<text v-if="d.advance!=undefined">
								元,停售前{{d.advance}}分钟截止投注</text>
						</view>
					</view>
					<view style="height: 125rpx;line-height: 125rpx;margin-left: 45rpx;"><uni-icons type="right"
							color="#9e9e9e"></uni-icons>
					</view>
				</view>
			</view>
		</view>
		<!-- 弹出层内容 -->
		<uni-popup ref="popup" type="bottom" :animation="true">
			<view style="background-color: #ffffff; margin-top: 30upx;">
				<view style="display: flex;justify-content: flex-end;align-items: center;padding: 30upx; 30upx 0px 0px"
					@click="that.$refs.popup.close()">
					<u-icon size="24" name="close"></u-icon>
				</view>
				<h4 style="text-align: center;">{{switchValue.name}}</h4>
				<view style="display: flex;align-items: center;justify-content: space-between;margin: 30upx 60upx">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">彩种上架</text>
						<text style="font-size: 12px;color: #9f9f9f;">上架后，客户端才能看到该彩种</text>
					</view>
					<view>
						<u-switch v-model="switch6" activeColor="#3999FE"
							@change="withdrawalChange(6,$event)"></u-switch>
					</view>
				</view>
				<view style="display: flex;align-items: center;justify-content: space-between;margin: 30upx 60upx">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">允许投注</text>
						<text style="font-size: 12px;color: #9f9f9f;">关闭后，用户将无法购买此彩种</text>
					</view>
					<view>
						<u-switch v-model="switch1" activeColor="#3999FE"
							@change="withdrawalChange(1,$event)"></u-switch>
					</view>
				</view>
				<view style="display: flex;align-items: center;justify-content: space-between;margin: 30upx 60upx">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">合作模式开关</text>
						<text style="font-size: 12px;color: #9f9f9f;">开启为合作模式自动甩单，关闭为合作模式手动甩单</text>
					</view>
					<view>
						<u-switch v-model="switch2" activeColor="#3999FE"
							@change="withdrawalChange(2,$event)"></u-switch>
					</view>
				</view>
				<view style="display: flex;align-items: center;justify-content: space-between;margin: 30upx 60upx" v-if="switchValue.name=='竞彩足球'||
				switchValue.name=='竞彩篮球'||
				switchValue.name=='北京单场'||
				switchValue.name=='胜负单场'">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">奖金优化</text>
						<text style="font-size: 12px;color: #9f9f9f;">开启后投注时可选择奖金优化</text>
					</view>
					<view>
						<u-switch v-model="switch3" activeColor="#3999FE"
							@change="withdrawalChange(3,$event)"></u-switch>
					</view>
				</view>
				<view style="display: flex;align-items: center;justify-content: space-between;margin: 0px 60upx 30upx">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">比赛截止时间(分钟)</text>
						<text style="font-size: 12px;color: #9f9f9f;">提前停止投注时间</text>
					</view>
					<view>
						<u-number-box v-model="switchValue.advance" min="-99999" integer
							@change="numberChange(1,$event)"></u-number-box>
					</view>
				</view>
				<view :style="switchValue.name!='冠军'&&
				switchValue.name!='冠亚军'?'margin: 0px 60upx 30upx':'margin:30upx 60upx 0px 60upx;padding-bottom: 30upx;'"
					style="display: flex;align-items: center;justify-content: space-between;">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">最小投注金额(元)</text>
						<text style="font-size: 12px;color: #9f9f9f;">允许的投注最低金额</text>
					</view>
					<view>
						<u-number-box v-model="switchValue.minPrice" min="2" integer
							@change="numberChange(2,$event)"></u-number-box>
					</view>
				</view>
				<view v-if="switchValue.name=='竞彩足球'||
				switchValue.name=='竞彩篮球'" :style="switchValue.name!='冠军'&&
				switchValue.name!='冠亚军'?'margin: 30upx 60upx;':''"
					style="display: flex;align-items: center;justify-content: space-between;">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">发单开关</text>
						<text style="font-size: 12px;color: #9f9f9f;">开启后，将允许此彩种发单/跟单功能</text>
					</view>
					<view>
						<u-switch v-model="switch4" activeColor="#3999FE"
							@change="withdrawalChange(4,$event)"></u-switch>
					</view>
				</view>

				<view v-if="switchValue.name=='竞彩足球'||
				switchValue.name=='竞彩篮球'" :style="switchValue.name!='冠军'&&
				switchValue.name!='冠亚军'?'margin: 30upx 60upx;':''"
					style="display: flex;align-items: center;justify-content: space-between;">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">最低发单金额</text>
						<text style="font-size: 12px;color: #9f9f9f;">最低发单金额</text>
					</view>
					<view>
						<u-number-box v-model="switchValue.issuingPrice" min="-99999" integer
							@change="numberChange(5,$event)"></u-number-box>
					</view>
				</view>

				<view v-if="switchValue.name!='冠军'&&
				switchValue.name!='冠亚军'"
					style="display: flex;align-items: center;justify-content: space-between;margin: 0 60upx;padding-bottom: 30upx;">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">合买开关</text>
						<text style="font-size: 12px;color: #9f9f9f;">开启后，将允许此彩种发布合买功能</text>
					</view>
					<view>
						<u-switch v-model="switch5" activeColor="#3999FE"
							@change="withdrawalChange(5,$event)"></u-switch>
					</view>
				</view>
				<view v-if="switchValue.name!='冠军'&&
				switchValue.name!='冠亚军'"
					style="display: flex;align-items: center;justify-content: space-between;margin: 0 60upx;padding-bottom: 30upx;">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">合买截止时间(分钟)</text>
						<text style="font-size: 12px;color: #9f9f9f;">提前截止合买时间</text>
					</view>
					<view>
						<u-number-box v-model="switchValue.togetherDeadline" min="-99999" integer
							@change="numberChange(3,$event)"></u-number-box>
					</view>
				</view>
				<view v-if="switchValue.name!='冠军'&&
				switchValue.name!='冠亚军'"
					style="display: flex;align-items: center;justify-content: space-between;margin: 0 60upx;padding-bottom: 30upx;">
					<view style="display: flex;flex-direction: column;">
						<text style="font-size: 16px;">发布合买最低金额</text>
						<text style="font-size: 12px;color: #9f9f9f;">发布合买最低金额</text>
					</view>
					<view>
						<u-number-box v-model="switchValue.togetherPrice" min="-99999" integer
							@change="numberChange(4,$event)"></u-number-box>
					</view>
				</view>
			</view>
		</uni-popup>
	</view>
</template>

<script>
	import {
		ballGameList,
		updateGame,
		updateLine
	} from '@/api/ballGame.js'
	export default {
		data() {
			return {
				that: this,
				switchValue: {},
				ballGameDataLine: [],
				ballGameDataUnLine: [],
				switch1: false,
				switch2: false,
				switch3: false,
				switch4: false,
				switch5: false,
				switch6: false,
			}
		},
		onLoad(option) {
			this.init();
		},
		methods: {
			numberChange(index, d) {
				if (index == 1) {
					this.switchValue.advance = d
				} else if (index == 2) {
					this.switchValue.minPrice = d
				} else if (index == 3) {
					this.switchValue.togetherDeadline = d
				} else if (index == 4) {
					this.switchValue.togetherPrice = d
				} else if (index == 5) {
					this.switchValue.issuingPrice = d
				}
				this.put()
			},
			put() {
				//提示弹窗，index==1代码点击了确定
				uni.showLoading({
					title: '修改中'
				})
				updateGame(this.switchValue).then(res => {
					if (res.success) {
						uni.showToast({
							title: '修改成功',
							icon: "success"
						})
						this.init();
						this.$refs.popup.close()
					}
				})
			},
			withdrawalChange(index, flag) {
				let text;
				if (flag) {
					text = "0";
				} else {
					text = "1";
				}
				if (index == 1) {
					this.switchValue.bet = text
				} else if (index == 2) {
					this.switchValue.cooperation = text
				} else if (index == 3) {
					this.switchValue.bonusOptimization = text
				} else if (index == 4) {
					this.switchValue.issuingSwitch = text
				} else if (index == 5) {
					this.switchValue.togetherSwitch = text
				} else if (index == 6) {
					this.switchValue.line = text
					updateLine(this.switchValue.id, this.switchValue.line).then(res => {
						if (res.success) {
							uni.showToast({
								title: '修改成功',
								icon: "success"
							})
							this.init();
							this.$refs.popup.close()
						}
					})
					return
				}
				this.put()
			},
			open(item, e) {
				this.switchValue = item
				this.switch1 = this.switchValue.bet == '0' ? true : false
				this.switch2 = this.switchValue.cooperation == '0' ? true : false
				this.switch3 = this.switchValue.bonusOptimization == '0' ? true : false
				this.switch4 = this.switchValue.issuingSwitch == '0' ? true : false
				this.switch5 = this.switchValue.togetherSwitch == '0' ? true : false
				this.switch6 = this.switchValue.line == '0' ? true : false
				this.$refs.popup.open('bottom')
			},
			init() {
				this.ballGameDataLine = [];
				this.ballGameDataUnLine = [];
				ballGameList().then((res) => {
					res.voList.map(item => {
						if (item.line != null && item.line != '' && item.line != undefined) {
							if (item.line == "0") {
								this.ballGameDataLine.push(item)
							} else if (item.line == "1") {
								this.ballGameDataUnLine.push(item)
							}
						}
					})
				})
			},
		}
	}
</script>

<style scoped>
	.topTitle {
		width: 100%;
		height: 50rpx;
		background-color: #eeeeee;
		line-height: 50rpx;
		position: -webkit-sticky;
		position: sticky;
		top: var(--status-bar-height);
		z-index: 2;
	}

	.biaoqian {
		width: 100%;
		height: 125rpx;
		border-bottom: 1rpx solid #eaeaea;
		display: flex;
		align-items: center;
		background: #ffffff;
	}

	.biaoqian:active {
		background-color: #f4f4f4;
	}

	.status_bar {
		background-color: white;
		height: var(--status-bar-height);
		width: 100%;
		position: -webkit-sticky;
		position: sticky;
		top: 0;
		z-index: 2;
	}
</style>