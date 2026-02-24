<template>
	<view class="box">
		<!-- 分段条 -->
		<zero-loading v-if="loading" mask></zero-loading>
		<u-back-top :scroll-top="scrollTop" :customStyle="{backgroundColor: '#3999FE'}"
			:iconStyle="{color: '#fff'}"></u-back-top>
		<view style="background-color: white;">
			<uni-search-bar @input="searchValue" placeholder="通过订单编号进行搜索" />
		</view>
		<u-line></u-line>
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
						<view style="color: rgb(134, 134, 134);" class="uni-body"
							@click.native.stop="copy(item.orderId)">
							订单编号：{{item.orderId}}
							<image src="../../static/image/personal/复制.png"
								style="width: 32upx;height: 32upx;padding-left: 20upx;margin-bottom: -8upx;">
							</image>
						</view>
						<view>下单用户：<span class="uni-body">{{item.nickname}}</span>
						</view>
						<view>订单状态：<span class="uni-body"
								:style="item.state=='3'||item.state=='4'?'color:#FF3F43':''">{{item.state|formatState}}</span>
						</view>
						<view>下单时间：<span class="uni-body">{{item.createTime|formatDate(that)}}</span>
						</view>
						<view style="color: #FF3F43;">预测最高奖金：<span class="uni-body">{{item.forecast}}</span>
						</view>
					</view>
					<view style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="item.notes+'注'" type="error"></u-tag>
						<u-tag :text="item.times +'倍'"></u-tag>
					</view>
				</view>
			</uni-card>
		</view>
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
				<p class="fz-13 fw500 p-t-15">时间选择</p>
				<div class="siftingBox">
					<uni-datetime-picker type="daterange" style='margin-top:20rpx;'
						@change='timeClick'></uni-datetime-picker>
				</div>
			</div>
			<div class="h-100vh p-t-15 p-l-20  pr">
				<p class="fz-13 fw500 p-t-15">订单状态</p>
				<div class="siftingBox">
					<div class="siftingBox">
						<div :style="check1===item.lable?'color:#3999FE;border: 1px solid #3999FE':''"
							@click="stateBtn(item)" class="siftingItem" v-for="(item,index) in stateActions"
							:key="index">
							{{item.lable}}
						</div>
					</div>
				</div>
			</div>
			<u-button style="position: fixed;bottom: 0;right: 0;width: 320px;" color="rgb(200, 200, 200)" text="重置"
				@click="clear"></u-button>
		</u-popup>
		<u-loadmore v-if="ishaveData" :line="true" status="nomore"></u-loadmore>
	</view>
</template>

<script>
	import {
		lotteryOrderList
	} from '@/api/lotteryOrder.js'
	export default {
		data() {
			return {
				scrollTop: 0,
				loading: false,
				ishaveData: false,
				show: false,
				stateActions: [{
					lable: "全部订单",
					value: ""
				}, {
					lable: "待出票",
					value: "0"
				}, {
					lable: "待开奖",
					value: "1"
				}, {
					lable: "未中奖",
					value: "2"
				}, {
					lable: "待兑奖",
					value: "3"
				}, {
					lable: "已兑奖",
					value: "4"
				}, {
					lable: "已退票",
					value: "6"
				}, {
					lable: "合买失败",
					value: "7"
				}, {
					lable: "合买中",
					value: "8"
				}],
				tita: ['店内订单', '接单订单', '甩单订单'],
				siftingItem: [{
					lable: "全部订单",
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
				check: "全部订单",
				check1: "全部订单",
				orderData: [],
				queryParam: {
					orderId: null,
					states: null,
					startTime: null,
					endTime: null,
					state:null,
					type: null,
					phone: null,
					classify: null,
					userId: null,
					pageNo: 1,
					pageSize: 8
				},
				orderData: [],
				that: this,
			}
		},
		onPageScroll(e) {
			this.scrollTop = e.scrollTop;
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
		onLoad(option) {
			if (option.state != undefined) {
				this.queryParam.state = option.state
				this.stateActions.forEach(item => {
					if (this.queryParam.state == item.value) {
						this.check1 = item.lable
					}
				})
			}
			if (option.userId != undefined) {
				this.queryParam.userId = option.userId
			}
			this.init()
		},
		filters: {
			//格式化日期
			formatDate(data, that) {
				if (data != null && data != undefined) {
					return that.globalUtil.dateTimeFormat(data)
				}
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
			}
		},
		methods: {
			clear() {
				this.queryParam.state = "";
				this.queryParam.type = "";
				this.queryParam.orderId = "";
				this.queryParam.startTime = "";
				this.queryParam.endTime = "";
				this.queryParam.phone = "";
				this.queryParam.classify = "";
				this.queryParam.pageNo = 1;
				this.init();
				this.show = false;
			},
			//点击卡片查询详情
			details(id) {
				uni.navigateTo({
					url: "/pages/order/lotteryOrderDetails?id=" + id,
					animationType: 'pop-in',
					animationDuration: 200
				})
			},
			timeClick(e) {
				this.queryParam.pageNo=1
				this.queryParam.startTime = e[0]
				this.queryParam.endTime = e[1]
				this.init()
				this.show = false;
			},
			copy(text) {
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
						title: "复制成功",
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
			searchValue(e) {
				this.queryParam.orderId = e;
				this.init();
			},
			init(type) {
				this.ishaveData = false;
				this.loading = true;
				lotteryOrderList(this.queryParam).then((res) => {
					if (type === 'loading') {
						this.orderData = [...this.orderData, ...res.voList]
					} else {
						this.orderData = res.voList == null ? [] : res.voList;
					}
					this.loading = false;
				}).catch(err => {
					this.loading = false;
				})
			},
			onNavigationBarButtonTap(event) {
				this.show = true
			},
			siftingBtn(item) {
				this.queryParam.pageNo=1;
				this.check = item.lable;
				this.queryParam.type = item.type;
				this.init();
				this.show = false;
			},
			stateBtn(item) {
				this.queryParam.pageNo=1
				this.check1 = item.lable;
				this.queryParam.state = item.value;
				this.init();
				this.show = false;
			},
		}
	}
</script>

<style scoped>
	.siftingBox {
		display: -webkit-box;
		display: -webkit-flex;
		-webkit-flex-wrap: wrap;
		flex-wrap: wrap;
		font-size: 23upx;
		text-align: center;
		display: flex;
		align-items: center;
		justify-content: space-around;
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

	.container {
		width: 96%;
		margin: 0 auto;
	}

	::v-deep .uni-card__content {
		padding: 0px !important;
	}

	::v-deep .uni-card {
		margin: 20upx 0px !important;
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

	.box1 {
		width: 186rpx;
		height: 80rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 28rpx;
		transition: .3s;
	}

	.navigation {
		width: 100%;
		height: 80rpx;
		background-color: #fff;
	}

	.bg {
		display: flex;
	}

	.xiabiao {
		position: absolute;
		width: 90rpx;
		height: 7rpx;
		background-color: #3999FE;
		/* z-index: -1; */
		border-radius: 50rpx;
		top: 73rpx;
		left: 140rpx;
		transition: .3s;
	}

	.p-l-20 {
		padding-left: 20upx !important;
	}

	.p-t-15 {
		padding-top: 20upx !important;
	}

	.fz-13 {
		font-size: 25upx !important;
	}

	page {
		background-color: #f3f3f3;
	}
</style>