<template>
	<view style="width: 94%;margin: 0 auto;">
		<u-back-top :scroll-top="scrollTop" :customStyle="{backgroundColor: '#3999FE'}"
			:iconStyle="{color: '#fff'}"></u-back-top>
		<u-form labelPosition="left" v-model="queryParam" labelWidth="80">
			<u-collapse>
				<u-collapse-item title="条件查询指南">
					<image style="width: 40px;height: 40px;" src="http://cmcpcp.test.upcdn.net/shouzhi4.gif"
						slot="icon"></image>
					<u-form-item label="起始时间" borderBottom>
						<uni-datetime-picker type="datetime" @change="startChange" v-model="queryParam.startTime" />
					</u-form-item>
					<u-form-item label="结束时间" borderBottom>
						<uni-datetime-picker type="datetime" @change="endChange" v-model="queryParam.endTime" />
					</u-form-item>
					<u-form-item label="手机号" borderBottom>
						<u-input maxlength="11" placeholder="请输入手机号" v-model="queryParam.phone" border="none"></u-input>
					</u-form-item>
					<u-form-item label="订单编号" borderBottom>
						<u-input v-model="queryParam.orderId" placeholder="请输入订单编号" border="none"></u-input>
					</u-form-item>
					<u-form-item label="店铺" borderBottom @click="showShop = true; hideKeyboard()">
						<u--input v-model="shopName" disabled disabledColor="#ffffff" placeholder="请选择店铺"
							border="none"></u--input>
						<u-icon slot="right" name="arrow-right"></u-icon>
					</u-form-item>
					<u-form-item label="订单状态" borderBottom @click="showState = true; hideKeyboard()">
						<u--input v-model="name" disabled disabledColor="#ffffff" placeholder="请选择订单状态"
							border="none"></u--input>
						<u-icon slot="right" name="arrow-right"></u-icon>
					</u-form-item>
					<u-form-item label="赛事类型" borderBottom @click="showType = true; hideKeyboard()">
						<u--input v-model="typeName" disabled disabledColor="#ffffff" placeholder="请选择赛事类型"
							border="none"></u--input>
						<u-icon slot="right" name="arrow-right"></u-icon>
					</u-form-item>
				</u-collapse-item>
			</u-collapse>
			<u-form-item>
				<u-button color="#3999FE" text="查询" @click="query()"></u-button>
				<u-button color="#3999FE" text="重置选择" customStyle="margin-left: 10px" @click="clear"></u-button>
			</u-form-item>
		</u-form>
		<u-picker  confirmColor="#3999FE" :show="showShop"
			:columns="shopColumns" keyName="name" @cancel="showShop=false" @confirm="shopConfirm"></u-picker>
		<!-- 订单状态选择组件 -->
		<u-picker :show="showState" :columns="stateActions" keyName="name" @cancel="showState=false"
			confirmColor="#3999FE" @confirm="stateSelect"></u-picker>

		<!-- 赛事类型选择组件 -->
		<u-picker :show="showType" :columns="typeActions" keyName="name" @cancel="showType=false" confirmColor="#3999FE"
			@confirm="typeSelect"></u-picker>
		</u-action-sheet>
		<u-empty :show="orderData.length<=0" mode="order" icon="http://cmcpcp.test.upcdn.net/order.png"
			text="暂无订单"></u-empty>
		<uni-card v-for="(item,index) in orderData" :key="index" :border="false" :title="item.ballName" is-shadow
			:style="[{animation: 'show ' + ((index+1)*0.2+1) + 's 1'}]" :sub-title="item.createTime|formatDate(that)"
			:extra="'订单'+item.price+'元'" :thumbnail="item.ballUrl">
			<view @click="details(item.id)">
				<!-- <view style="text-align: center;">
					<image style="width: 40px;height: 40px;" src="http://cmcpcp.test.upcdn.net/daaea5f1dcea6a4384e527c5bb6754b5.gif"></image>
				</view> -->
				<view>所属店铺：<span class="uni-body">{{item.shopName}}</span></view>
				<view>订单编号：<span class="uni-body">{{item.orderId}}</span></view>
				<view>选号用户：<span class="uni-body">{{item.nickname}}</span></view>
				<view>上级：<span class="uni-body">{{item.parentName}}</span></view>
				<view style="color: #FF3F43;">预测奖金：<span class="uni-body">{{item.forecast}}</span></view>
				<view>是否需要实体票：<span class="uni-body">{{item.isActualTicket=='0'?'否':'是'}}</span></view>
				<view>出票时间：<span class="uni-body">{{item.ticketingTime|formatDate(that)}}</span></view>
				<view>订单状态：<span class="uni-body"
						:style="item.state=='3'||item.state=='4'?'color:#FF3F43':''">{{item.state|formatState}}</span>
				</view>
				<view v-if="item.winPrice!=undefined">中奖金额：<span class="uni-body" style="font-size: 18px;"
						:style="'color:#FF3F43'" v-if="item.state=='3'||item.state=='4'">{{item.winPrice}}</span></view>
			</view>
		</uni-card>
		<u-loadmore v-if="ishaveData" :line="true" status="nomore"></u-loadmore>
	</view>
</template>

<script>
	import {
		correctOrderList,
	} from '@/api/correct.js'
	import {
		shopList,
	} from '@/api/shop.js'
	export default {
		data() {
			return {
				showShop: false,
				shopColumns: [],
				shopName:"",
				that: this,
				typeActions: [
					[{
						name: "竞彩足球",
						value: "0"
					}, {
						name: "竞彩篮球",
						value: "1"
					}, {
						name: "北京单场",
						value: "2"
					}, {
						name: "排列3",
						value: "3"
					}, {
						name: "排列5",
						value: "4"
					}, {
						name: "七星彩",
						value: "5"
					}, {
						name: "足球14场",
						value: "6"
					}, {
						name: "任选九",
						value: "7"
					}, {
						name: "大乐透",
						value: "8"
					}, {
						name: "胜负过关",
						value: "9"
					}]
				],
				stateActions: [
					[{
						name: "待出票",
						value: "0"
					}, {
						name: "待开奖",
						value: "1"
					}, {
						name: "未中奖",
						value: "2"
					}, {
						name: "待兑奖",
						value: "3"
					}, {
						name: "已兑奖",
						value: "4"
					}, {
						name: "已退票",
						value: "6"
					}, {
						name: "合买失败",
						value: "7"
					}, {
						name: "合买中",
						value: "8"
					}],
				],
				showState: false,
				showType: false,
				name: "",
				typeName: "",
				queryParam: {
					orderId: "",
					startTime: "",
					endTime: "",
					state: "",
					type: "",
					phone: "",
					shopId:"",
					pageNo: 1,
					pageSize: 15
				},
				orderData: [],
				ishaveData: false,
				scrollTop: 0,
			}
		},
		//滚动到底部进行分页事件
		onReachBottom() {
			if (this.orderData.length < this.queryParam.pageNo * 10) {
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
		filters: {
			//格式化日期
			formatDate(data, that) {
				if (data != null && data != undefined) {
					return that.globalUtil.dateTimeFormat(data)
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
		onLoad() {
			this.init();
			this.shopList();
		},
		methods: {
			query() {
				this.queryParam.pageNo = 1;
				this.init();
			},
			startChange(e) {
				this.queryParam.startTime = new Date(e);
			},
			endChange(e) {
				this.queryParam.endTime = new Date(e);
			},
			//点击卡片查询详情
			details(id) {
				uni.navigateTo({
					url: "/pages/correct/lotteryOrderDetails?id=" + id,
					animationType: 'pop-in',
					animationDuration: 200
				})
			},
			hideKeyboard() {
				uni.hideKeyboard()
			},
			stateSelect(e) {
				this.name = e.value[0].name;
				this.queryParam.state = e.value[0].value
				this.showState = false;
			},
			shopConfirm(e) {
				this.shopName = e.value[0].name;
				this.queryParam.shopId = e.value[0].id
				this.showShop = false;
			},
			typeSelect(e) {
				this.typeName = e.value[0].name;
				this.queryParam.type = e.value[0].value
				this.showType = false;
			},
			init(type) {
				this.ishaveData = false;
				correctOrderList(this.queryParam).then((res) => {
					if (type === 'loading') {
						this.orderData = [...this.orderData, ...res.voList]
					} else {
						this.orderData = res.voList == null ? [] : res.voList;
					}
				})
			},
			shopList(){
				shopList({
					pageNo: 1,
					pageSize: 999999999		
				}).then(r => {
					this.shopColumns = [];
					this.shopColumns.push(r.voList);
				})
			},
			clear() {
				this.name = "";
				this.typeName = "";
				this.shopName = "";
				this.queryParam.state = "";				
				this.queryParam.shopId = "";
				this.queryParam.type = "";
				this.queryParam.orderId = "";
				this.queryParam.startTime = "";
				this.queryParam.endTime = "";
				this.queryParam.phone = "";
				this.queryParam.pageNo = 1;
				this.init();
			},
		}
	}
</script>

<style scoped lang="scss">
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

	::v-deep .u-tag {
		margin-right: 15px !important;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	::v-deep .u-loadmore {
		padding-bottom: 20px !important;
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