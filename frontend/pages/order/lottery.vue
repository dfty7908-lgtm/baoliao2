<template>
	<view>
		<zero-loading v-if="loading" mask></zero-loading>
		<u-back-top :scroll-top="scrollTop" :customStyle="{backgroundColor: '#3999FE'}"
			:iconStyle="{color: '#fff'}"></u-back-top>
		<u-tabs :list="tabsList" :activeStyle="{color: '#3999FE',fontWeight: 'bold'}" :current="selectIndex"
			lineWidth="60" lineColor="#3999FE" @change="changeSelectBall"></u-tabs>

		</u-action-sheet>
		<u-empty :show="orderData.length<=0" mode="order" icon="http://cmcpcp.test.upcdn.net/order.png"
			text="暂无订单"></u-empty>
		<view class="container" :style="loading?'width:100%':'width:96%'">
			<uni-card @click="details(item.id)" v-for="(item,index) in orderData" :key="index" :border="false"
				:title="item.ballName" is-shadow shadow="0px 0px 8px 1px rgba(0, 0, 0, 0.1)"
				:style="[{animation: 'show ' + ((index+1)*0.2+1) + 's 1'}]"
				:sub-title="item.createTime|formatDate(that,1)" :extra="'订单'+item.price+'元'" :thumbnail="item.ballUrl">
				<view class="wanfa" v-if="item.meetShopId!=null">
					<uni-tag :mark="true" text="合作" type="primary" size="mini" />
				</view>
				<view class="wanfa" v-if="item.orderType=='1'">
					<uni-tag :mark="true" text="合买" type="warning" size="mini" />
				</view>
				<view class="wanfa" v-if="item.documentaryType!=''&&item.documentaryType!=null">
					<uni-tag :mark="true" :text="item.documentaryType=='0'?'发单':'跟单'" type="success" size="mini" />
				</view>
				<view style="display: flex;justify-content: space-between;align-items: center;">
					<view>
						<!-- <view style="text-align: center;">
						<image style="width: 40px;height: 40px;" src="http://cmcpcp.test.upcdn.net/daaea5f1dcea6a4384e527c5bb6754b5.gif"></image>
					</view> -->
						<view v-if="item.meetShopId!=null&&item.meetShopId!=''">
							{{ item.meetShopId == shop.id ? '推单店铺' : '甩单店铺' }}：<span
								class="uni-body">{{item.sellShopName}}</span>
						</view>
						<view>订单状态：<span class="uni-body"
								:style="item.state=='3'||item.state=='4'?'color:#FF3F43':''">{{
									item.isReceive=='0'?'待接单':formatState(item.state) }}</span>
						</view>
						<view v-if="item.winPrice!=undefined">中奖金额：<span class="uni-body" style="font-size: 18px;"
								:style="'color:#FF3F43'"
								v-if="item.state=='3'||item.state=='4'">{{item.winPrice}}</span>
						</view>
						<view :style="'color:#FF3F43'">截止时间：<span
								class="uni-body">{{item.deadline|formatDate(that)}}</span>
						</view>
						<view style="color: #FF3F43;">预测最高奖金：<span class="uni-body">{{item.forecast}}</span>
						</view>
					</view>
					<view style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="item.notes+'注'" type="error"></u-tag>
						<u-tag :text="item.times +'倍'"></u-tag>
					</view>
				</view>
				<u-line style="padding: 10px 0px;"></u-line>
				<view style="padding-bottom: 10px;">
					<view v-if="item.isReceive=='1'" style="display: flex;justify-content: center;align-items: center">
						<u-button :plain="true" :hairline="true"
							v-if="item.state=='3'&&item.meetShopId==null||item.state=='3'&&item.meetShopId==shop.id"
							color="#3999FE" text="兑奖" customStyle="margin-left: 10px"
							@click.native.stop="handleAward(item)"></u-button>
						<u-button v-if="item.isReceive=='1'&&item.state=='0'" :plain="true" :hairline="true"
							color="#3999FE" text="去出票" @click.native.stop="details(item.id)" />
						<u-button :plain="true" :hairline="true" color="#3999FE"
							v-if="item.isMeet=='1'&&item.meetShopId==null&&item.state=='0'" text="甩单"
							customStyle="margin-left: 10px" @click.native.stop="sell(item)"></u-button>
					</view>
					<view style="display: flex;justify-content: center;align-items: center">
						<u-button :plain="true" :hairline="true"
							v-if="item.isReceive=='0'&&item.classify!='3'&&item.buyState!='2'" color="#3999FE" text="接单"
							@click.native.stop="reject(item,'1')"></u-button>
						<u-button :plain="true" :hairline="true" v-if="item.buyState=='2'" color="#3999FE" text="合买中"
							@click.native.stop="hemai(item)"></u-button>
						<u-button :plain="true" :hairline="true"
							v-if="item.meetShopId!=null&&item.isReceive=='0'&&item.meetShopId==shop.id" color="#3999FE"
							text="驳回" customStyle="margin-left: 10px" @click.native.stop="reject(item,'0')"></u-button>
					</view>
				</view>
			</uni-card>
		</view>
		<u-picker @cancel="dialogSellVisible=false" @confirm="sellShop" :show="dialogSellVisible" closeOnClickOverlay
			:columns="columns" keyName="name" @close="dialogSellVisible=false"></u-picker>
		<u-popup :show="show" mode="right" @close="show=false"
			:customStyle="{width:'320px',borderRadius:'15px 0px 0px 15px'}">
			<div class="h-100vh p-t-15 p-l-20  pr">
				<p class="fz-13 fw500 p-t-15">彩种筛选</p>
				<div class="siftingBox">
					<div :style="check===item.lable?'color:#3999FE;border: 1px solid #3999FE':''"
						@click="siftingBtn(item)" class="siftingItem" v-for="(item,index) in siftingItem" :key="index">
						{{item.lable}}
					</div>
				</div>
			</div>
			<div class="h-100vh p-t-15 p-l-20  pr">
				<p class="fz-13 fw500 p-t-15">订单排序</p>
				<div class="siftingBox">
					<div :style="check1===item.lable?'color:#3999FE;border: 1px solid #3999FE':''"
						@click="stateBtn(item)" class="siftingItem" v-for="(item,index) in stateActions" :key="index">
						{{item.lable}}
					</div>
				</div>
			</div>
			<u-button style="position: fixed;bottom: 0;right: 0;width: 320px;" color="rgb(200, 200, 200)" text="重置"
				@click="reset"></u-button>
		</u-popup>
	</view>
</template>

<script>
	import {
		sellList,
		sellShop,
		reject
	} from '@/api/cooperation.js'
	import {
		lotteryOrderList,
		statistics
	} from '@/api/lotteryOrder.js'
	import cfg from '@/util/environment.js';
	import {
		delFile
	} from '@/api/upload.js'
	export default {
		data() {
			return {
				show: false,
				selectIndex: 0,
				tabsList: [{
					name: '待接单',
					badge: {
						value: 0,
					}
				}, {
					name: '待出票',
					badge: {
						value: 0,
					}
				}, {
					name: '合买',
					badge: {
						value: 0,
					}
				}, {
					name: '合作',
					badge: {
						value: 0,
					}
				}],
				loading: false,
				awardShow: false,
				that: this,
				rejectParam: {
					id: "",
					type: ""
				},
				item: {},
				siftingItem: [{
					lable: "全部彩种",
					type: ""
				}, {
					lable: "竞彩足球",
					type: 0
				}, {
					lable: "竞彩篮球",
					type: 1
				}, {
					lable: "北京单场",
					type: 2
				}, {
					lable: "排列3",
					type: 3
				}, {
					lable: "排列5",
					type: 4
				}, {
					lable: "七星彩",
					type: 5
				}, {
					lable: "胜负彩",
					type: 6
				}, {
					lable: "任选九",
					type: 7
				}, {
					lable: "大乐透",
					type: 8
				}, {
					lable: "胜负过关",
					type: 9
				}, {
					lable: "福彩3D",
					type: 10
				}, {
					lable: "双色球",
					type: 11
				}, {
					lable: "七乐彩",
					type: 12
				}, {
					lable: "快乐8",
					type: 13
				}],
				awardActions: [
					[{
						name: "未中奖",
						value: "2"
					}, {
						name: "已中奖",
						value: "3"
					}]
				],
				stateActions: [{
					lable: "下单时间",
					value: ""
				}, {
					lable: "截止时间",
					value: "deadline"
				}],
				awardDisabled: true,
				queryParam: {
					orderId: "",
					states: "",
					startTime: "",
					endTime: "",
					state: "",
					type: "",
					phone: "",
					classify: "",
					userId: null,
					pageNo: 1,
					pageSize: 8,
					isReceive: "0",
					orderByDeadline: ""
				},
				dialogSellVisible: false,
				orderData: [],
				ishaveData: false,
				scrollTop: 0,
				disable: true,
				shop: {},
				check: "全部彩种",
				check1: "下单时间",
				columns: [],
				timer: null,
			}
		},
		//滚动到底部进行分页事件
		onReachBottom() {
			if (this.orderData.length < this.queryParam.pageNo * this.queryParam.pageSize) {
				this.ishaveData = true;
				return;
			}
			this.queryParam.pageNo++;
			this.init("loading");
		},
		onPullDownRefresh() {
			this.orderData = []
			this.queryParam.pageNo = 1;
			this.init();
			uni.stopPullDownRefresh()
		},
		onPageScroll(e) {
			this.scrollTop = e.scrollTop;
		},
		onNavigationBarButtonTap(event) {
			this.show = true
		},
		filters: {
			//格式化日期
			formatDate(data, that, type) {
				if (data != null && data != undefined) {
					if (type == 1) {
						return '下单时间：' + that.globalUtil.dateTimeFormat(data)
					} else {
						return that.globalUtil.dateTimeFormat(data)
					}
				}
			},
			formatterPaiType(type) {
				if (type == "0") {
					return "直选"
				} else if (type == "1") {
					return "组三"
				} else if (type == "2") {
					return "组六"
				} else if (type == "3") {
					return "直选和值"
				} else if (type == "4") {
					return "组选和值"
				} else if (type == "5") {
					return "组三单式"
				}
			},
		},
		onShow() {
			this.skd()
			this.init()
			this.statistics()
			if (this.timer == null) {
				this.timer = setInterval(() => {
					if (this.queryParam.pageNo == 1) {
						this.init()
					}
					// this.statistics()
				}, 5000)
			}
		},
		onLoad(options) {
			this.$initializePage();
			this.queryParam.isReceive = "0";
			this.shop = uni.getStorageSync("shop");
			let state = options.state;
			let orderId = options.orderId;
			let obj = options.obj;
			let userId = options.userId;
			//从工作台过来的
			if (state != undefined) {
				this.queryParam.state = state;
			}
			//从跟单页面过来的
			if (orderId != undefined) {
				this.queryParam.orderId = orderId;
			}
			if (userId != undefined) {
				this.queryParam.userId = userId;
			}
			if (options.item != undefined) {
				let item = JSON.parse(decodeURIComponent(options.item))
				this.queryParam.startTime = item.startTime
				this.queryParam.endTime = item.endTime
				this.queryParam.states = item.state
			}
			//从分类统计过来的
			if (obj != undefined) {
				obj = JSON.parse(decodeURIComponent(obj));
				this.queryParam.type = obj.type;
				this.queryParam.state = "5,6";
				this.queryParam.startTime = obj.startTime;
				this.queryParam.endTime = obj.endTime;
			}
		},
		/* onTabItemTap() {
			this.queryParam.isReceive = "0";
			this.selectIndex=0;
			this.clear();
		}, */
		onHide() {
			// 清理定时器  
			if (this.timer) {
				clearInterval(this.timer);
				this.timer = null; // 避免后续引用  
			}
		},
		methods: {
			skd(){
				this.queryParam.type = uni.getStorageSync("siftingType")
				this.queryParam.orderByDeadline = uni.getStorageSync("orderByDeadline")
				if (this.queryParam.type === '') {
					this.check = "全部彩种"
					this.queryParam.type = ""
				} else {
					this.siftingItem.filter(item => {
						if (item.type === this.queryParam.type) {
							this.check = item.lable
							return
						}
					})
				}
				if (this.queryParam.orderByDeadline === '') {
					this.check1 = "下单时间"
					this.queryParam.orderByDeadline = ""
				} else {
					this.stateActions.filter(item => {
						if (item.value === this.queryParam.orderByDeadline) {
							this.check1 = item.lable
							return
						}
					})
				}
			},
			reset() {
				uni.removeStorageSync("orderByDeadline")
				uni.removeStorageSync("siftingType")
				this.check = "全部彩种"
				this.check1 = "下单时间"
				this.queryParam.type=null;
				this.queryParam.orderByDeadline=null
				this.init();
			},
			hemai(item) {
				uni.navigateTo({
					url: "/pages/purchase/info?id=" + item.jointPurchaseId
				})
			},
			//格式化状态
			formatState(data) {
				if (data == 0) {
					return "待出票";
				} else if (data == 1) {
					return "待开奖";
				} else if (data == 2) {
					return "未中奖";
				} else if (data == 3) {
					return "待兑奖";
				} else if (data == 4) {
					return "已兑奖";
				} else if (data == 5) {
					return "已拒绝";
				} else if (data == 6) {
					return "已退票";
				} else if (data == 7) {
					return "合买失败";
				} else if (data == 8) {
					return "合买中";
				}
			},
			stateBtn(item) {
				this.queryParam.pageNo=1
				this.check1 = item.lable;
				this.queryParam.orderByDeadline = item.value;
				this.init();
				this.show = false;
				uni.setStorageSync("orderByDeadline", item.value)
			},
			siftingBtn(item) {
				this.queryParam.pageNo=1
				this.check = item.lable;
				this.queryParam.type = item.type;
				this.init();
				this.show = false;
				uni.setStorageSync("siftingType", item.type)
			},
			statistics() {
				statistics().then(res => {
					this.tabsList[0].badge.value = res.isReceiveCount
					this.tabsList[1].badge.value = res.isTicketCount
					this.tabsList[2].badge.value = res.isBuyCount
					this.tabsList[3].badge.value = res.isCooperationCount
				})
				// if (!this.timer) {
				// 	this.timer = setInterval(() => {
				// 		statistics().then(res => {
				// 			this.tabsList[0].badge.value = res.isReceiveCount
				// 			this.tabsList[1].badge.value = res.isTicketCount
				// 			this.tabsList[2].badge.value = res.isBuyCount
				// 			this.tabsList[3].badge.value = res.isCooperationCount
				// 		})
				// 	}, 3000)
				// }
			},
			changeSelectBall(item) {
				this.selectIndex = item.index
				this.queryParam.pageNo = 1
				this.clear()
				this.skd()
				if (item.index == 0) {
					this.queryParam.isReceive = "0"
				} else if (item.index == 1) {
					this.queryParam.state = "0"
					this.queryParam.isReceive = "1"
				} else if (item.index == 2) {
					this.queryParam.isReceive = null
					this.queryParam.orderType = "1"
				} else if (item.index == 3) {
					this.queryParam.state = "0"
					this.queryParam.classify = "2"
				}
				this.init()
			},
			reject(item, type) {
				this.rejectParam.id = item.id;
				this.rejectParam.type = type;
				reject(this.rejectParam).then(res => {
					if (res.success) {
						if (type == "1") {
							uni.navigateTo({
								url: "/pages/order/lotteryOrderDetails?id=" + item.id + "&abo=1",
								animationType: 'pop-in',
								animationDuration: 200
							})
						} else {
							uni.showToast({
								title: "操作成功",
								icon: "success"
							})
							this.init()
						}
					}
				})
			},
			sell(item) {
				sellList(this.shop.uid, item.type).then(res => {
					if (res.voList.length == 1) {
						uni.showModal({
							title: '温馨提醒',
							confirmColor: "#3999FE",
							content: '确定要甩单（' + res.voList[0].name + '）店铺吗？',
							success: r => {
								if (r.confirm) {
									sellShop(item.id, res.voList[0].shopId).then(res => {
										uni.showToast({
											title: "操作成功",
											icon: "success"
										})
										this.init()
									})
								}
							}
						});
					} else if (res.voList.length > 1) {
						this.orderId = item.id
						this.columns = [];
						this.columns.push(res.voList)
						this.dialogSellVisible = true;
					}
				})
			},
			//点击卡片查询详情
			details(id) {
				uni.navigateTo({
					url: "/pages/order/lotteryOrderDetails?id=" + id,
					animationType: 'pop-in',
					animationDuration: 200
				})
			},
			init(type) {
				this.ishaveData = false;
				lotteryOrderList(this.queryParam).then((res) => {
					// res.voList.map(item => {
					// 	if (item.type != '3' && item.type != '4' && item.type != '5' && item.type != '8' &&
					// 		item.type != '10' &&
					// 		item.type != '11' &&
					// 		item.type != '12' &&
					// 		item.type != '13'
					// 	) {
					// 		item.racingBallList.map(racingBall => {
					// 			racingBall.content = JSON.parse(racingBall.content);
					// 		})
					// 	}
					// })
					if (type === 'loading') {
						this.orderData = [...this.orderData, ...res.voList]
					} else {
						this.orderData = res.voList == null ? [] : res.voList;
					}
					this.loading = false
				}).catch(err => {
					this.loading = false
				})
			},
			clear() {
				this.queryParam.state = null;
				this.queryParam.states = null;
				this.queryParam.type = null;
				this.queryParam.orderId = null;
				this.queryParam.orderType = null;
				this.queryParam.startTime = null
				this.queryParam.endTime = null;
				this.queryParam.phone = null;
				this.queryParam.userId = null
				this.queryParam.classify = null
				this.queryParam.isReceive = null
				this.queryParam.orderByDeadline = null;
				this.queryParam.pageNo = 1;
				this.init();
			},
			sellShop(item) {
				uni.showModal({
					title: '温馨提醒',
					confirmColor: "#3999FE",
					content: '确定要甩单（' + item.value[0].name + '）店铺吗？',
					success: r => {
						if (r.confirm) {
							sellShop(this.orderId, item.value[0].shopId).then(res => {
								uni.showToast({
									title: "操作成功",
									icon: "success"
								})
								this.dialogSellVisible = false;
								this.init()
							})
						}
					}
				});
			},
		}
	}
</script>

<style scoped lang="scss">
	.u-tabs {
		background: #ffffff;
	}

	.siftingBox {
		display: -webkit-box;
		display: -webkit-flex;
		-webkit-flex-wrap: wrap;
		flex-wrap: wrap;
		font-size: 23upx;
		text-align: center;
		display: flex;
		align-items: center;
		gap: 20upx;
		justify-content: flex-start;
	}

	.siftingItem {
		height: 58upx;
		line-height: 58upx;
		margin-top: 34upx;
		width: 70px;
		padding: 0 5px;
		border: 1px solid #979797;
		-webkit-border-radius: 5px;
		border-radius: 5px;
		color: #979797;
	}

	.p-l-20 {
		padding: 20upx 40upx;
	}

	.p-t-15 {
		padding-top: 20upx !important;
	}

	.fz-13 {
		font-size: 25upx !important;
	}

	::v-deep .u-tabs__wrapper__nav__item {
		flex: 1 1 0% !important;
	}

	page {
		background: #F3F3F3;
	}

	.container {
		margin: 0 auto;
	}

	.wanfa {
		position: absolute;
		top: 0;
		left: 0;
		z-index: 10;
	}

	::v-deep .uni-card__header-extra-text {
		font-size: 15px !important;
		color: #FF3F43 !important;
	}

	::v-deep .u-popup .u-transition {
		z-index: 998 !important;
	}

	::v-deep .uni-card__content {
		padding: 0px !important;
	}

	::v-deep .uni-card {
		margin: 15px 0px !important;
	}

	::v-deep .uni-card:active {
		transition: .2s;
		transform: scale(0.9);
	}

	::v-deep .u-tag {
		margin-right: 10upx !important;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	::v-deep .u-loadmore {
		padding-bottom: 40upx !important;
	}

	::v-deep .u-upload__deletable {
		background-color: #FF3F43;
		height: 30px;
		width: 30px;
	}

	::v-deep .u-icon__icon.uicon-close {
		font-size: 25px !important;
		line-height: 22px !important;
	}
</style>