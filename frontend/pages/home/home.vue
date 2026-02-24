<template>
	<view class="box">
		<swiper class="swiper">
			<swiper-item v-for="(one, index) of banners" :key="index" @click="swiperBtn">
				<image previous-margin="20upx" next-margin="20upx" class="banner" :src="one" mode="widthFix"></image>
			</swiper-item>
		</swiper>
		<!-- 日期选择 -->
		<view style="width:100%;background-color: #ffffff;display: flex;padding:10rpx 0rpx;">
			<view class="deta-text" @click="openPicker">
				<text style="font-size: 24rpx;">{{username}}</text>
				<uni-icons type="bottom"></uni-icons>
			</view>
			<uni-datetime-picker type="daterange" :border="false" :clear-icon="false" v-model="range"
				@change='clickTimePicker' />
		</view>
		<!-- 上半部分 -->
		<view style="width:720rpx;margin:20rpx auto;background-color: white;border-radius:15rpx;padding-bottom:20rpx;">
			<view class="topCard">
				<view class="pj" @click="pageSkip(0)">
					<text style="color:white;font-weight:bold;font-size:40rpx;">{{satistcsData.totalBetting}}</text>
					<text style="font-size: 26rpx; font-weight:normal;">出票总金额</text>
				</view>
				<view class="subsection"></view>
				<view class="pj" @click="pageSkip(1)">
					<text
						style="color:white;font-weight:bold;font-size:40rpx;">{{satistcsData.alreadyAwardPrice}}</text>
					<text style="font-size: 26rpx; font-weight:normal;">中奖总金额(已派奖)</text>
				</view>
			</view>
			<!-- 因为list默认带一条分隔线，所以使用 titleBorder="none" 取消面板的分隔线 -->
			<uni-collapse-item title-border="none" :border="false" :show-animation="true">
				<template v-slot:title>
					<uni-list>
						<view style="font-size:26rpx;padding:20rpx 10rpx;">
							<text style="">店主彩民跟单佣金1%:</text>
							<text style="color: red;">￥0.00</text>
						</view>
					</uni-list>
				</template>
				<view class="content">
					<view class="content-e">
						<text class="yrad">￥0.00</text>
						<text style="font-weight: bold;padding-top: 10rpx;">发单人佣金</text>
					</view>
					<view>:</view>
					<view class="content-e">
						<text class="yrad">￥0.00</text>
						<text style="font-weight: bold;padding-top: 10rpx;">本店</text>
					</view>
					<view>+</view>
					<view class="content-e">
						<text class="yrad">￥0.00</text>
						<text style="font-weight: bold;padding-top: 10rpx;">其他店</text>
					</view>
				</view>
			</uni-collapse-item>
			</uni-collapse>
			<view style="display: flex;justify-content: space-around;height:120rpx;align-items: center;">
				<view style="display: flex;align-items: center;" @click="pageSkip(0)">
					<image src="@/static/image/personal/出票.png" style="width: 50rpx;height: 50rpx;"></image>
					<text style="font-size: 28rpx;padding-left: 20rpx;">出票详情</text>
				</view>
				<view style="display: flex;align-items: center;" @click="pageSkip(1)">
					<image src="@/static/image/personal/中奖.png" style="width: 50rpx;height: 50rpx;"></image>
					<text style="font-size: 28rpx;padding-left: 20rpx;">中奖详情</text>
					<text style="font-size: 20rpx; color: #5e5e5e ;">(已派奖)</text>
				</view>
			</view>
		</view>

		<view style="width:720rpx;margin:20rpx auto;">
			<text style="font-size: 28rpx;margin-left: 10rpx;">对账汇总</text>
			<view class="accountCard">
				<view style="line-height: 45rpx;">
					<text style="font-size: 26rpx;color: #5e5e5e;">托管余额</text><br>
					<text style="color:red;font-weight:bold;font-size:36rpx;">{{totalPrice}}</text>
				</view>
				<view style="line-height: 45rpx;">
					<text style="font-size: 26rpx;color: #5e5e5e;">服务费支出</text><br>
					<text style="color:red;font-weight:bold;font-size:36rpx;">{{satistcsData.serviceCharge}}</text>

				</view>
			</view>
		</view>

		<view style="width: 720rpx;height: 130rpx;margin:20rpx auto;">
			<text style="font-size: 28rpx;margin-left: 10rpx;">加扣款统计</text>
			<view class="statisticsCard">
				<view class="jia_kuan" style="color: #FDC830;" @click="toLink(0)">
					<text style="font-size: 26rpx;">加款金额</text>
					<text style="color:#FDC830;font-weight:bold;">{{satistcsData.shopAddPrice}}</text>
				</view>
				<view class="jia_kuan" style="color: #ff7979;" @click="toLink(1)">
					<text style="font-size: 26rpx;">扣款金额</text>
					<text style="color:#ff7979;font-weight:bold;">{{satistcsData.shopReducePrice}}</text>
				</view>
			</view>
		</view>

		<view class="rechargeCard">
			<navigator :url="'/pages/disaggregation/rechargeStatistics'" open-type="navigate" hover-class="none"
				style="display: flex;justify-content: space-between;height:80rpx;align-items: center;">
				<view style="display: flex;align-items: center;">
					<image src="@/static/image/personal/jiaqian.png"
						style="width: 40rpx;height: 40rpx;margin-right: 20rpx;" />
					<text style="font-size: 28rpx;">充值总金额</text>
				</view>

				<view style="display: flex;align-items: center;">
					<text style="color:red;font-weight:bold;">{{satistcsData.totalRechargePrice}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</navigator>

			<navigator :url="'/pages/withdrawal/withdrawalHistory'" open-type="navigate" hover-class="none"
				style="display: flex;justify-content: space-between;height:80rpx;align-items: center;">
				<view style="display: flex;align-items: center;">
					<image src="@/static/image/personal/jiaqian.png"
						style="width: 40rpx;height: 40rpx;margin-right: 20rpx;" />
					<text style="font-size: 28rpx;">提现总金额</text>
				</view>
				<view style="display: flex;align-items: center;">
					<text style="color:red;font-weight:bold;">{{satistcsData.totalWithdrawalPrice}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</navigator>
		</view>

		<view class="ul with-li-cards with-have-no-mt with-have-no-click-bg">
			<view class="title-box">
				<view class="title">账单统计</view>
				<text class="iconfont icon-right"></text>
			</view>
			<uni-row class="nav-list">
				<uni-col :span="12" :key="index" v-for="(item,index) in billArr">
					<view hover-class='none' class="nav-li" @click="btn(index)"
						:style="'background:'+item.color+';'+(index%2==0?'margin-right:10px':'margin-left:10px')">
						<view class="nav-title">{{
								index==0?satistcsData.userCount==undefined?0:satistcsData.userCount+'个':
								index==1?satistcsData.registerCount==undefined?0:satistcsData.registerCount+'个':
								index==2?satistcsData.realCount==undefined?0:satistcsData.realCount+'个':
								index==3?satistcsData.rechargePrice==undefined?0:satistcsData.rechargePrice+'元':
								index==4?satistcsData.bettingPrice==undefined?0:satistcsData.bettingPrice+'元':
								index==5?satistcsData.withdrawalPrice==undefined?0:satistcsData.withdrawalPrice+'元':
								index==6?satistcsData.yesterdayBettingPrice==undefined?0:satistcsData.yesterdayBettingPrice+'元':
								index==7?satistcsData.yesterdayAwardPrice==undefined?0:satistcsData.yesterdayAwardPrice+'元':
								index==8?satistcsData.totalBetting==undefined?0:satistcsData.totalBetting+'元':
								index==9?satistcsData.alreadyAwardPrice==undefined?0:satistcsData.alreadyAwardPrice+'元':
								index==10?satistcsData.awardPrice==undefined?0:satistcsData.awardPrice+'元':
								index==11?satistcsData.totalPrice==undefined?0:satistcsData.totalPrice+'元':
								index==12?satistcsData.totalRechargePrice==undefined?0:satistcsData.totalRechargePrice+'元':
								satistcsData.totalWithdrawalPrice==undefined?0:satistcsData.totalWithdrawalPrice+'元'}}</view>
						<view class="nav-name">{{item.name}}</view>
						<u-icon class="icon" :name="item.icon" size="31" color="#fff"></u-icon>
					</view>
				</uni-col>
			</uni-row>
		</view>
		<!-- 弹窗选择器 -->
		<u-picker :show="showDian" :columns="lineFormColumns" keyName="username" confirmColor="#3999FE"
			@cancel="showDian=false" @confirm="lineFormConfirm"></u-picker>
	</view>
</template>

<script>
	import {
		getUser,
		total
	} from '@/api/user.js';
	import {
		pageList
	} from '@/api/sysUser.js';
	import {
		statistics
	} from '@/api/statistics.js'
	export default {
		data() {
			return {
				banners: [
					require("@/static/image/home/mybj11.png")
				],
				username: "全部",
				showDian: false,
				queryParam: {
					startTime: "",
					filtrate: "",
					endTime: "",
					skip: null,
					id: null,
				},
				statisticsParam: {
					pageNo: 1,
					pageSize: 1000,
					pid: null,
				},
				lineFormColumns: [],
				range: "",
				satistcsData: {},
				totalPrice: 0,
				counter: {},
				billArr: [{
						'name': '用戶总量',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '今日注册',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '今日实名',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '今日预存',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '今日投注',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '今日提现',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '昨日投注',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '昨日兑奖',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '总投注',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '总已兑奖金额',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '待兑奖金额',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '用户账户总额',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '总预存',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '总提现',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '昨日盈利',
						'color': '#3999FE',
						'icon': 'coupon'
					},
					{
						'name': '总盈利',
						'color': '#3999FE',
						'icon': 'coupon'
					},

				],
			}
		},
		onLoad() {
			this.init()
			this.$initializePage();
		},
		onPullDownRefresh() {
			this.init();
			uni.stopPullDownRefresh()
		},
		onTabItemTap() {
			this.init();
		},
		methods: {
			toLink(e) {
				uni.navigateTo({
					url: "/pages/disaggregation/billStatistics?selectIndex=" + e
				})
			},
			pageSkip(e) {
				uni.navigateTo({
					url: "/pages/disaggregation/disaggregation?pageState=" + e
				})
			},
			clickTimePicker(item) {
				this.queryParam.startTime = item[0]
				this.queryParam.endTime = item[0]
				this.init()
			},
			lineFormConfirm(item) {
				this.username = item.value[0].username
				this.queryParam.id = item.value[0].id
				statistics(this.queryParam).then(res => {
					this.satistcsData = res;
				})
				this.showDian = false;
			},
			openPicker() {
				this.showDian = true;
				this.lineFormColumns = []
				pageList(this.statisticsParam).then(res => {
					if (res.voList.length <= 0) {
						this.lineFormColumns.push([{
							username: "全部",
							id: null,
						}])
					} else {
						this.lineFormColumns.push(res.voList)
						this.lineFormColumns[0].unshift({
							username: "全部",
							id: null,
						});
					}
				})
			},
			btn(index) {
				const today = new Date();
				// 设置开始时间为今天的 00:00:00  
				const startTime = new Date(today.getFullYear(), today.getMonth(), today.getDate(), 0, 0, 0);
				// 设置结束时间为今天的 23:59:59  
				const endTime = new Date(today.getFullYear(), today.getMonth(), today.getDate(), 23, 59, 59);
				if (index == 0) {
					uni.reLaunch({
						url: "/pages/user/user"
					});
				} else if (index == 1 || index == 2) {
					let isReal = "";
					if (index == 2) {
						isReal = "1"
					}
					uni.reLaunch({
						url: "/pages/user/user?item=" + encodeURIComponent(JSON.stringify({
							startTime,
							endTime,
							isReal
						}))
					});
				} else if (index == 3) {
					uni.reLaunch({
						url: "/pages/order/pay?item=" + encodeURIComponent(JSON.stringify({
							startTime,
							endTime,
							type: "0"
						}))
					});
				} else if (index == 4) {
					uni.reLaunch({
						url: "/pages/order/lottery?item=" + encodeURIComponent(JSON.stringify({
							startTime,
							endTime,
							state: "0,1,2,3,4"
						}))
					});
				} else if (index == 5) {
					uni.navigateTo({
						url: "/pages/withdrawal/withdrawal?item=" + encodeURIComponent(JSON.stringify({
							startTime,
							endTime,
							state: "1"
						}))
					});
				} else if (index == 6) {
					uni.reLaunch({
						url: "/pages/order/lottery?item=" + encodeURIComponent(JSON.stringify({
							startTime: new Date(startTime.setDate(startTime.getDate() - 1)),
							endTime: new Date(endTime.setDate(endTime.getDate() - 1)),
							state: "0,1,2,3,4"
						}))
					});
				} else if (index == 7) {
					uni.reLaunch({
						url: "/pages/order/lottery?item=" + encodeURIComponent(JSON.stringify({
							startTime: new Date(startTime.setDate(startTime.getDate() - 1)),
							endTime: new Date(endTime.setDate(endTime.getDate() - 1)),
							state: "3,4"
						}))
					});
				} else if (index == 8) {
					uni.reLaunch({
						url: "/pages/order/lottery?item=" + encodeURIComponent(JSON.stringify({
							state: "0,1,2,3,4"
						}))
					});
				} else if (index == 9) {
					uni.reLaunch({
						url: "/pages/order/lottery?item=" + encodeURIComponent(JSON.stringify({
							state: "3,4"
						}))
					});
				} else if (index == 10) {
					uni.reLaunch({
						url: "/pages/order/lottery?item=" + encodeURIComponent(JSON.stringify({
							state: "3"
						}))
					});
				} else if (index == 11) {
					uni.navigateTo({
						url: "/pages/order/detail?selectIndex=0"
					});
				} else if (index == 12) {
					uni.navigateTo({
						url: "/pages/order/detail?selectIndex=1"
					});
				} else if (index == 13) {
					uni.navigateTo({
						url: "/pages/order/detail?selectIndex=2"
					});
				}
			},
			init() {
				getUser().then(res => {
						this.user = res
						if (res.adminShop.state != '2') {
							uni.removeStorageSync("stop")
						}
						this.statisticsParam.pid = res.id
					}),
					statistics(this.queryParam).then(res => {
						this.satistcsData = res;
					})
				total().then(res => {
					this.totalPrice = res.totalPrice.toFixed(2);
				})
				//动态设置app背景颜色
				var meta = document.querySelector('meta[name="theme-color"]');
				if (null != meta) {
					meta.content = '#3999FE';
				} else {
					var script = document.createElement('meta');
					script.name = "theme-color";
					script.content = '#3999FE';
					document.head.appendChild(script)
				}
			},
		}
	}
</script>

<style scoped>
	.nav-list {
		display: flex;
		flex-wrap: wrap;
		padding: 0px 40upx 0px;
		justify-content: space-between;
	}

	.nav-title {
		font-size: 19px;
		color: #fff;
		font-weight: bold;
		padding-left: 30upx;
	}

	.nav-name {
		font-size: 28upx;
		color: #fff;
		text-transform: Capitalize;
		margin-top: 20upx;
		position: relative;
		padding-left: 30upx;
	}

	.nav-name::before {
		content: "";
		position: absolute;
		display: block;
		width: 40upx;
		height: 6upx;
		background: #fff;
		bottom: 0;
		right: 0;
		opacity: 0.5;
	}

	.nav-name::after {
		content: "";
		position: absolute;
		display: block;
		width: 100upx;
		height: 1px;
		background: #fff;
		bottom: 0;
		right: 40upx;
		opacity: 0.3;
	}

	.icon {
		position: absolute;
		right: 30upx;
		top: 30upx;
		font-size: 52upx;
		width: 60upx;
		height: 60upx;
		text-align: center;
		line-height: 60upx;
		opacity: 0.15;
	}

	.nav-li {
		padding: 30upx 0px;
		border-radius: 12upx;
		margin: 0 0px 40upx 0px;
		/* background-image: url(https://i.imgtg.com/2022/12/15/Dsef6.jpg); */
		background-size: cover;
		background-position: center;
		position: relative;
		z-index: 1;
	}

	.nav-li::after {
		content: " ";
		position: absolute;
		z-index: -1;
		width: 100%;
		height: 100%;
		left: 0;
		bottom: 0;
		border-radius: inherit;
		opacity: 1;
		-webkit-transform: scale(1, 1);
		transform: scale(1, 1);
		background-size: 100% 100%;
		background-image: url(@/static/image/home/2.png);
	}

	.ul .title-box {
		padding: 10upx;
		font-size: 28upx;
		color: #000000;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.swiper {
		margin: 20upx 14upx;
		border-radius: 4%;
	}

	.banner {
		width: 100% !important;
		height: 100% !important;
		border-radius: 4%;
	}

	.staff {
		width: 100%;
		height: 500rpx;
		background-color: white;
		padding: 20rpx 0rpx;
		border-top-left-radius: 30rpx;
		border-top-right-radius: 30rpx;
	}

	.statisticsCard {
		background-color: white;
		border-radius: 15rpx;
		display: flex;
		padding: 20rpx 0rpx;
		justify-content: space-around;
	}

	.rechargeCard {
		display: flex;
		width: 680rpx;
		padding: 20rpx;
		background-color: #ffffff;
		margin: 20rpx auto;
		border-radius: 15rpx;
		flex-direction: column;
	}

	.accountCard {
		background-color: white;
		border-radius: 15rpx;
		padding: 20rpx;
		display: flex;
		justify-content: space-around;
		text-align: center;
	}

	.subsection {
		width: 2rpx;
		height: 50rpx;
		background-color: #ffffff;
		border-radius: 15rpx;
		box-shadow: #f3ce6f 0px 0px 2px 0.5px;
	}

	.status_bar {
		background-color: #fff;
		height: var(--status-bar-height);
		width: 100%;
	}

	body {
		background-color: #f4f4f4;
	}

	.topCard {
		width: 720rpx;
		height: 150rpx;
		border-radius: 15rpx;
		background: linear-gradient(140deg, #A0D1F5, #3999FE);
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.pj {
		width: 350rpx;
		height: 150rpx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		color: #fff;
	}

	.pj text {
		padding-top: 10rpx;
		font-size: 38rpx;
		font-weight: bold;
	}

	.content {
		height: 100rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		margin: auto;
	}

	.content-e {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		margin: auto;
		width: 200rpx;
	}

	.deta-text {
		width: 160rpx;
		height: 68rpx;
		background-color: #f0f0f0;
		border-radius: 15rpx;
		margin: 0 20rpx;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.yrad {
		color: #ff0000;
		line-height: 30rpx;
	}

	.jia_kuan {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
</style>