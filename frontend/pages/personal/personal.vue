<template>
	<view>
		<!-- #ifdef APP-PLUS -->
		<uni-scroll-view class="content" refresher-enabled @refresh="minList">
		<!-- #endif -->
			<view class="yellowBack">
				<view class="topCard">
					<!-- 店铺卡片 -->
					<view style="display: flex;padding:30rpx 0rpx 30rpx 30rpx;">
						<view @click="tiaozhuan" style="margin-right:20rpx;">
							<image v-if="user.adminShop!=undefined" :src="user.adminShop.logo" mode="aspectFill"
								class="logoStyle" />
						</view>
						<view style="height:110rpx;display: flex;flex-flow: column;justify-content: space-between;">
							<view style="font-weight: bold;">
								<text v-if="user.adminShop!=undefined">{{user.adminShop.name}}</text>
								<text style="font-size:16rpx;font-weight: normal;" v-if="user.adminShop!=undefined">
									(ID.{{user.adminShop.uid}})
								</text>
							</view>
							<view style="display: flex;" v-if="user.adminShop!=undefined &&user.adminShop.state!='2'">
								<view class="authenticationFalse">
									<uni-icons type="medal-filled" color="white" />
									<text style="font-size:26rpx;color: white;font-weight: bold;">
										未认证
									</text>
								</view>
							</view>
							<view v-else style="display: flex;">
								<view class="authenticationTrue">
									<uni-icons type="medal-filled" color="white" />
									<text style="font-size:26rpx;color: white;font-weight: bold;">已认证</text>
								</view>
							</view>
						</view>
						<view class="tuiguan" @click="shareButtom">
							<uni-icons type="paperplane-filled" size="28rpx" color="white"></uni-icons>
							<text style="font-size:28rpx;padding-left:5rpx;">店铺推广</text>
						</view>
					</view>
					<view style="display: flex;justify-content: space-between;">
						<navigator url="/pages/shop/wallet" hover-class="none">
							<view style="text-align: center;margin-left:80rpx;">
								<text style="font-size:28rpx;">店铺余额</text><br>
								<text style="color: red;font-weight:bold;"
									v-if="user.adminShop!=undefined">{{user.adminShop.balance}}</text>
							</view>
						</navigator>
						<view style="width:2rpx; height:40rpx;background-color:#3999FE;align-self:center;"></view>
						<navigator url="/pages/order/detail" hover-class="none">
							<view style="text-align: center;margin-right:80rpx;">
								<text style="font-size:28rpx;">托管余额</text><br>
								<text style="color: red;font-weight:bold;">
									{{totalPrice}}
								</text>
							</view>
						</navigator>
					</view>
				</view>
			</view>

			<view class="yika">
				<!-- 第一排 -->
				<view class="tita">
					<text>常用功能</text>
				</view>

				<view class="yineirenBoxNo">
					<view v-for="item,index in imgData" :key="index" class="clickBotton"
						@click="btn(index,item.navigator)">
						<uni-badge :text="statisticsData.ticketingCount" absolute="rightTop"
							v-show="item.text=='等待出票' && statisticsData.ticketingCount>0" :offset="[-40,-40]"
							style="z-index:2;" />
						<uni-badge :text="statisticsData.awardCount" absolute="rightTop"
							v-show="item.text=='等待派奖' && statisticsData.awardCount>0" :offset="[-40,-40]"
							style="z-index:2;" />
						<uni-badge :text="statisticsData.withdrawalCount" absolute="rightTop"
							v-show="item.text=='提现申请' && statisticsData.withdrawalCount>0" :offset="[-40,-40]"
							style="z-index:2;" />
						<image :src="item.src" class="imgSize" v-show="true">
						</image><br>
						<text style="font-size:28rpx;" v-show="true">
							{{item.text}}
						</text>
					</view>
				</view>
			</view>
			<view class="erCard">
				<view class="tita">
					<text>店铺管理</text>
				</view>
				<view class="sanCardNeir">
					<view style="text-align:center;" v-for="item,index in erCardImg" :key="index" v-show="true"
						@click="btn('',item.navigator)">
						<uni-badge :text="cooperationCount" absolute="rightTop"
							v-show="item.text=='合作店铺' && cooperationCount>0" :offset="[-40,-40]" style="z-index:2;" />
						<uni-badge :text="statisticsData.depositCount" absolute="rightTop"
							v-show="item.text=='加账管理' && statisticsData.depositCount>0" :offset="[-40,-40]"
							style="z-index:2;" />
						<uni-badge :text="unreadCount" absolute="rightTop" v-show="item.text=='社交管理' && unreadCount>0"
							:offset="[-40,-40]" style="z-index:2;" />
						<image :src="item.src" class="imgSize"></image><br>
						<text style="font-size:26rpx;">{{item.text}}</text>
					</view>
				</view>
			</view>
			<view class="sanCard">
				<!-- 第三排 -->
				<view class="tita">
					<text>店铺服务</text>
				</view>
				<view class="sanCardNeir">
					<view v-for="item,index in sanCardImg" :key="index" style="text-align: center;"
						@click="btn('',item.navigator)">
						<image :src="item.src" class="imgSize"></image><br>
						<text style="font-size:26rpx;">{{item.text}}</text>
					</view>
				</view>
			</view>
		<!-- #ifdef APP-PLUS -->
		</uni-scroll-view>
		<!-- #endif -->
	</view>
</template>
<script>
	import {
		getUser,
		userStatistics,
		total
	} from '@/api/user.js'
	import {
		domainQuery
	} from '@/api/domain.js'
	import {
		statistics
	} from '@/api/statistics.js'
	import {
		recordExamineCount
	} from '@/api/cooperation'
	export default {
		data() {
			return {
				user: {},
				totalPrice: 0,
				unreadCount: 0,
				imgData: [{
						src: require('@/static/image/personal/订单.png'),
						navigator: '/pages/order/orderLnquiry',
						text: '订单查询',
					},
					{
						src: require('@/static/image/personal/出票.png'),
						navigator: '/pages/order/daiChuPiaoPage?state=0',
						text: '等待出票',
					},
					{
						src: require('@/static/image/personal/中奖.png'),
						text: '等待派奖',
						navigator: '/pages/order/daiPaiJianPage?state=3'
					},
					{
						src: require('@/static/image/personal/提现.png'),
						text: '提现申请',
						navigator: '/pages/withdrawal/withdrawal'
					},
				],
				erCardImg: [{
						src: require('@/static/image/personal/店铺.png'),
						text: '店铺设置',
						navigator: '/pages/personal/detail'
					}, {
						src: require('@/static/image/personal/收款.png'),
						text: '收款管理',
						navigator: '/pages/payment/payment'
					},
					{
						src: require('@/static/image/personal/规则.png'),
						text: '彩种管理',
						navigator: '/pages/ballgame/set'
					},
					{
						src: require('@/static/image/personal/员工.png'),
						text: '员工管理',
						navigator: '/pages/sys/sysUser'
					},
					{
						src: require('@/static/image/personal/公告.png'),
						text: '公告管理',
						navigator: '/pages/ballgame/notice'
					}, {
						src: require('@/static/image/personal/日志.png'),
						text: '日志管理',
						navigator: '/pages/log/log'
					}, {
						src: require('@/static/image/personal/比赛.png'),
						text: '比赛管理',
						navigator: '/pages/competition/competition'
					}, {
						src: require('@/static/image/personal/跟单.png'),
						text: '跟单管理',
						navigator: '/pages/hall/hall'
					}, {
						src: require('@/static/image/personal/合买.png'),
						text: '合买管理',
						navigator: '/pages/purchase/hall'
					}
					// , {
					// 	src: require('@/static/image/personal/社交.png'),
					// 	text: '社交管理',
					// 	navigator: '/pages/chat/list'
					// }
					, {
						src: require('@/static/image/personal/合作.png'),
						text: '合作店铺',
						navigator: '/pages/cooperation/cooperation'
					}, {
						src: require('@/static/image/personal/jiaqian.png'),
						text: '加账管理',
						navigator: '/pages/deposit/deposit'
					}, {
						src: require('@/static/image/personal/流水.png'),
						text: '流水管理',
						navigator: '/pages/order/pay'
					}
				],
				sanCardImg: [{
					src: require('@/static/image/personal/推送.png'),
					text: '推送管理',
					navigator: '/pages/email/email'
				}, {
					src: require('@/static/image/personal/轮播图.png'),
					text: '轮播管理',
					navigator: '/pages/banner/banner'
				}, {
					src: require('@/static/image/personal/导航.png'),
					text: '导航管理',
					navigator: '/pages/navigation/navigation'
				}, {
					src: require('@/static/image/personal/主题.png'),
					text: '主题管理',
					navigator: '/pages/theme/theme'
				}, {
					src: require('@/static/image/personal/pc.png'),
					text: '电脑端',
					navigator: ''
				}],
				queryParam: {
					startTime: "",
					filtrate: "",
					endTime: "",
					skip: "1",
				},
				statisticsData: {},
				cooperationCount: 0,
				pcAdminUrl: ""
			}
		},
		methods: {
			clickCopy(text) { //点击需要复制的内容
				// #ifdef APP-PLUS
				uni.setClipboardData({
					String(text),
					success: () => {
						uni.showToast({
							title: "复制成功",
							icon: "success"
						})
					},
					fail: () => {
						uni.showToast({
							title: "复制失败",
							icon: "error"
						})
					}
				});
				// #endif
				// #ifdef H5
				this.$copyText(String(text)).then(() => {
					uni.showToast({
						title: "复制成功,请前往电脑打开",
						icon: "success"
					})
				}, () => {
					uni.showToast({
						title: "复制失败",
						icon: "error"
					})
				})
				// #endif

			},
			shareButtom() {
				uni.navigateTo({
					url: "/pages/personal/shareCanvas"
				})
			},
			tiaozhuan() {
				uni.navigateTo({
					url: "/pages/personal/detail"
				})
			},
			init() {
				getUser().then(res => {
					this.user = res
					recordExamineCount(res.adminShop.uid).then(res => {
						this.cooperationCount = res.count;
					})
				})
				total().then(res => {
					this.totalPrice = res.totalPrice.toFixed(2);
				})
				statistics(this.queryParam).then(res => {
					this.statisticsData = res;
				})
				domainQuery().then(res => {
					this.pcAdminUrl = res.pcAdminUrl
				})
			},
			btn(index, url) {
				if (url == '') {
					this.clickCopy(this.pcAdminUrl)
					return;
				}
				uni.navigateTo({
					url
				})
			},
		},
		onShow() {
			this.init()
		},
		onLoad() {
			this.$initializePage();
		}
	}
</script>

<style scoped>
	.imgPupop {
		width: 680rpx;
		height: 680rpx;
		border-radius: 15rpx;
		background-color: white;
		display: flex;
		flex-flow: column;
		align-items: center;
		justify-content: center;
	}

	.explain {
		color: #d0d0d0;
		position: absolute;
		bottom: 20rpx;
	}

	.update {
		padding: 2rpx 10rpx;
		border-top-left-radius: 15rpx;
		border-bottom-right-radius: 15rpx;
		background-color: red;
		color: white;
		font-size: 24rpx;
		font-weight: bold;
		margin: 0rpx 10rpx;
	}

	.yineirenBoxNo {
		display: flex;
		justify-content: space-between;
		text-align: center;
	}

	.logoStyle {
		/* border-radius:120rpx; */
		width: 120rpx;
		height: 120rpx;
		border-radius: 10rpx;
	}

	.clickBotton:active {
		transition: .2s;
		transform: scale(0.9);
	}

	.clickBotton {
		margin: 0rpx 20rpx;
	}

	.authenticationTrue {
		padding: 0rpx 20rpx;
		height: 45rpx;
		background-color: #3999FE;
		border-radius: 10rpx;
		text-align: center;
		line-height: 45rpx;
	}

	.authenticationFalse {
		padding: 0rpx 20rpx;
		height: 45rpx;
		background: linear-gradient(to bottom right, #d4d4d4, #4c4c4c);
		border-radius: 10rpx;
		text-align: center;
		line-height: 45rpx;
	}

	.sanCardNeir {
		display: grid;
		grid-template-columns: repeat(4, 120rpx);
		grid-template-rows: repeat(1, 120rpx);
		grid-row-gap: 40rpx;
		justify-content: space-between;
	}

	.sanCard {
		width: 680rpx;
		background-color: white;
		padding: 20rpx;
		border-radius: 15rpx;
		margin: 10rpx auto;
	}

	.tita {
		padding: 0rpx 0rpx 10rpx 0rpx;
		font-weight: 700;
		font-size: 30rpx;
	}

	.erCard {
		width: 680rpx;
		background-color: white;
		padding: 20rpx;
		border-radius: 15rpx;
		margin: 15rpx auto;
	}

	.imgSize {
		width: 70rpx;
		height: 70rpx;
		align-self: center;
	}

	.yika {
		width: 680rpx;
		background-color: white;
		padding: 20rpx;
		border-radius: 15rpx;
		margin: 15rpx auto;
	}

	body {
		background-color: #f3f3f3;
	}

	.tuiguan {
		height: 70rpx;
		text-align: center;
		margin-left: auto;
		margin-top: 20rpx;
		background: linear-gradient(to bottom right, #3999FE, #A0D1F5);
		color: #ffffff;
		font-weight: bold;
		border-top-left-radius: 10rpx;
		border-bottom-left-radius: 10rpx;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.topCard {
		height: 290rpx;
		background-color: white;
		border-radius: 20rpx;
		margin: var(--status-bar-height) auto;
	}

	.yellowBack {
		width: 100%;
		background: linear-gradient(140deg, #3999FE, #A0D1F5);
		border-radius: 0 0 20rpx 20rpx;
		padding: 10rpx;
		box-sizing: border-box;
	}
</style>