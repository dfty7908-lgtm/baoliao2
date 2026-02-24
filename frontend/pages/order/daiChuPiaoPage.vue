<template>
	<view class="box">
		<!-- 分段条 -->
		<zero-loading v-if="loading" mask></zero-loading>
		<u-back-top :scroll-top="scrollTop" :customStyle="{backgroundColor: '#3999FE'}"
			:iconStyle="{color: '#fff'}"></u-back-top>
		<u-form labelPosition="left" v-model="queryParam" labelWidth="80" style="background: #fff;">
			<u-form-item style="width: 96%;margin: 0 auto;">
				<u-button color="#3999FE" :plain="true" :hairline="true" text="一键出票" @click="confirm"></u-button>
			</u-form-item>
		</u-form>
		<u-empty :show="orderData.length<=0" mode="order" icon="http://cmcpcp.test.upcdn.net/order.png"
			text="暂无订单"></u-empty>
		<view class="container" :style="loading?'width:100%':'width:96%'">
			<view style="display: flex;justify-content: center;align-items: center;" v-for="(item,index) in orderData"
				:key="index">
				<uni-card @click="details(item.id)" :border="false" :title="item.ballName" is-shadow
					shadow="0px 0px 8px 1px rgba(0, 0, 0, 0.1)"
					:style="[{animation: 'show ' + ((index+1)*0.2+1) + 's 1'}]"
					:sub-title="item.createTime|formatDate(that,1)" :extra="'订单'+item.price+'元'"
					:thumbnail="item.ballUrl">
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
									:style="item.state=='3'||item.state=='4'?'color:#FF3F43':''">{{
										item.isReceive=='0'?'待接单':formatState(item.state) }}</span>
							</view>
							<view v-if="item.winPrice!=undefined">中奖金额：<span class="uni-body" style="font-size: 18px;"
									:style="'color:#FF3F43'"
									v-if="item.state=='3'||item.state=='4'">{{item.winPrice}}</span>
								<view>下单时间：<span class="uni-body">{{item.createTime|formatDate(that)}}</span>
								</view>
							</view>
						</view>
						<view style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
							<u-tag :text="item.notes+'注'" type="error"></u-tag>
							<u-tag :text="item.times +'倍'"></u-tag>
						</view>
					</view>
					<u-line style="padding: 10px 0px;"></u-line>
					<view style="padding-bottom: 10px;">
						<view v-if="item.isReceive=='1'"
							style="display: flex;justify-content: center;align-items: center">
							<u-button :plain="true" :hairline="true" color="#3999FE" text="去出票"
								@click.native.stop="details(item.id)" />
						</view>
						<view style="display: flex;justify-content: center;align-items: center">
							<u-button :plain="true" :hairline="true" v-if="item.isReceive=='0'&&item.classify!='3'"
								color="#3999FE" text="接单" @click.native.stop="reject(item,'1')"></u-button>
							<u-button :plain="true" :hairline="true"
								v-if="item.meetShopId!=null&&item.isReceive=='0'&&item.meetShopId==shop.id"
								color="#3999FE" text="驳回" customStyle="margin-left: 10px"
								@click.native.stop="reject(item,'0')"></u-button>
						</view>
					</view>
				</uni-card>
			</view>
		</view>
		<u-loadmore v-if="ishaveData" :line="true" status="nomore"></u-loadmore>
	</view>
</template>

<script>
	import {
		sellList,
		reject
	} from '@/api/cooperation.js'
	import {
		tick,
		lotteryOrderList,
	} from '@/api/lotteryOrder.js'
	export default {
		data() {
			return {
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
					pageSize: 8
				},
				rejectParam: {
					id: "",
					type: ""
				},
				ticketing: {
					state: "",
					id: "",
				},
				orderData: [],
				that: this,
				scrollTop: 0,
				loading: false,
				ishaveData: false,
			}
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
		},
		onNavigationBarButtonTap(event) {
			uni.navigateTo({
				url: "/pages/order/orderLnquiry?state=1"
			})
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
			this.queryParam.state = option.state
		},
		onShow() {
			this.init()
		},
		methods: {
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
			confirm() {
				uni.showModal({
					title: '提示',
					confirmColor: "#3999FE",
					content: '只能一键出客户不需要店主上传实票的订单,则需要实票的订单需要店主手动出票?',
					success: res => {
						if (res.confirm) {
							tick(this.ticketing).then(res => {
								if (res.success) {
									uni.showToast({
										title: "操作成功",
										icon: "success"
									})
									this.init()
								}
							})
						} else {
							this.awardDisabled = true;
						}
					}
				});
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
		}
	}
</script>

<style scoped>
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
		padding-bottom: 140upx !important;
	}

	page {
		background-color: #f3f3f3;
	}
</style>