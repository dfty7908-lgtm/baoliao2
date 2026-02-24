<template>
	<view style="width: 94%;margin: 0 auto;">
		<u-back-top :scroll-top="scrollTop" :customStyle="{backgroundColor: '#3999FE'}"
			:iconStyle="{color: '#fff'}"></u-back-top>
		<u-collapse>
			<u-collapse-item title="条件查询指南" style="background: #fff;">
				<image style="width: 40px;height: 40px;" src="http://cmcpcp.test.upcdn.net/shouzhi4.gif" slot="icon">
				</image>
				<u-form labelPosition="left" v-model="queryParam" labelWidth="80">
					<u-form-item label="手机号" borderBottom>
						<u-input maxlength="11" placeholder="请输入手机号" v-model="queryParam.phone" border="none"></u-input>
					</u-form-item>
					<u-form-item label="订单编号" borderBottom>
						<u-input placeholder="请输入订单编号" v-model="queryParam.orderId" border="none"></u-input>
					</u-form-item>
					<u-form-item label="起始时间" borderBottom>
						<uni-datetime-picker type="datetime" @change="startChange" v-model="queryParam.startTime" />
					</u-form-item>
					<u-form-item label="结束时间" borderBottom>
						<uni-datetime-picker type="datetime" @change="endChange" v-model="queryParam.endTime" />
					</u-form-item>
					<u-form-item label="订单状态" borderBottom @click="showOrderStatus = true; hideKeyboard()">
						<u--input v-model="orderStatusName" disabled disabledColor="#ffffff" placeholder="请选择订单状态"
							border="none"></u--input>
						<u-icon slot="right" name="arrow-right"></u-icon>
					</u-form-item>
					<u-form-item label="订单类型" borderBottom @click="showOrderType = true; hideKeyboard()">
						<u--input v-model="orderTypeName" disabled disabledColor="#ffffff" placeholder="请选择订单类型"
							border="none"></u--input>
						<u-icon slot="right" name="arrow-right"></u-icon>
					</u-form-item>
					<u-form-item>
						<u-button color="#3999FE" text="查询" @click="query()" customStyle="margin-left: 10px"></u-button>
						<u-button color="#3999FE" text="重置选择" customStyle="margin-left: 10px" @click="clear"></u-button>
					</u-form-item>
				</u-form>
			</u-collapse-item>
		</u-collapse>
		<u-empty :show="orderData.length<=0" mode="data" icon="http://cmcpcp.test.upcdn.net/data.png"
			text="暂无数据"></u-empty>
		<uni-card v-for="(item,index) in orderData" :key="index" :border="false" :title="item.nickname" is-shadow
			:style="[{animation: 'show ' + ((index+1)*0.2+1) + 's 1'}]" :sub-title="item.createTime|formatDate(that)"
			:extra="item.type|formatterType" :thumbnail="item.avatar">
			<view>订单编号：<span class="uni-body">{{item.orderId}}</span></view>
			<view>支付方式：<span class="uni-body">{{item.payType|formatterPayType}}</span></view>
			<view>金额：<span class="uni-body">{{item.price}} 元</span></view>
			<view>支付状态：<span class="uni-body">{{item.state|formatterState}}</span></view>
			<view>修改时间：<span class="uni-body">{{item.updateTime|formatDate(that)}}</span></view>
			<u-line style="padding: 10px 0px;"></u-line>
			<!-- <view style="display: flex;justify-content: center;align-items: center;padding-bottom: 10px;">
				<u-button color="#3999FE" text="删除订单"
					@click="delPayOrderHandle(item)"></u-button>
			</view> -->
		</uni-card>
		<u-picker :show="showOrderStatus" :columns="orderStatusColumns" keyName="label" @cancel="showOrderStatus=false"
			confirmColor="#3999FE" @confirm="orderStatusConfirm"></u-picker>

		<u-picker :show="showOrderType" :columns="orderTypeColumns" keyName="label" confirmColor="#3999FE"
			@cancel="showOrderType=false" @confirm="orderTypeConfirm"></u-picker>

		<u-keyboard default="" :showCancel="false" :showConfirm="false" @change="keyboardChange" @backspace="backspace"
			:mask="true" @close="clear" :mask-close-able="false" tips="流水密码" :dot-enabled="false"
			:safe-area-inset-bottom="true" :tooltip="true" ref="uKeyboard" mode="number" :dotDisabled="true"
			:show="dialogLockVisible">
			<view
				style="display: flex;justify-content: center;align-items:  center;flex-direction: column;background: #fff;padding: 20px 0px 0px;">
				<u-code-input bold space="20" fontSize="20" size="40" :value="lockParam.password"
					:disabled-keyboard='true'></u-code-input>
			</view>
		</u-keyboard>
		<u-loadmore v-if="ishaveData" :line="true" status="nomore"></u-loadmore>
	</view>
</template>

<script>
	import {
		payOrderList,
		delPayOrder
	} from '@/api/pay.js'
	import {
		checkFlowingWaterPwd
	} from '@/api/dict.js'
	export default {
		data() {
			return {
				orderTypeName: "",
				showOrderStatus: false,
				orderStatusName: "",
				showOrderType: false,
				that: this,
				orderTypeColumns: [
					[{
							label: '账户预存',
							type: "0"
						}, {
							label: '选号竞彩足球（扣除）',
							type: "1"
						},
						{
							label: '选号竞彩篮球（扣除）',
							type: "2"
						},
						{
							label: '选号北京单场（扣除）',
							type: "3"
						},
						{
							label: '选号排列3（扣除）',
							type: "4"
						},
						{
							label: '提现打款',
							type: "5"
						},
						{
							label: '竞彩足球（兑奖）',
							type: "6"
						},
						{
							label: '竞彩篮球（兑奖）',
							type: "7"
						},
						{
							label: '北京单场（兑奖）',
							type: "8"
						},
						{
							label: '排列3（兑奖）',
							type: "9"
						},
						{
							label: '竞彩足球（退票）',
							type: "10"
						},
						{
							label: '竞彩篮球（退票）',
							type: "11"
						},
						{
							label: '北京单场（退票）',
							type: "12"
						},
						{
							label: '排列3（退票）',
							type: "13"
						},
						{
							label: '选号排列5',
							type: "15"
						},
						{
							label: '排列5兑奖',
							type: "16"
						},
						{
							label: '排列5退票',
							type: "17"
						},
						{
							label: '选号七星彩',
							type: "18"
						},
						{
							label: '七星彩兑奖',
							type: "19"
						},
						{
							label: '七星彩退票',
							type: "20"
						},
						{
							label: '选号足球14场',
							type: "21"
						},
						{
							label: '足球14场兑奖',
							type: "22"
						},
						{
							label: '足球14场退票',
							type: "23"
						},
						{
							label: '选号任选九',
							type: "24"
						},
						{
							label: '任选九兑奖',
							type: "25"
						},
						{
							label: '任选九退票',
							type: "26"
						},
						{
							label: '选号大乐透',
							type: "27"
						},
						{
							label: '大乐透兑奖',
							type: "28"
						},
						{
							label: '大乐透退票',
							type: "29"
						},
						{
							label: '选号胜负过关',
							type: "30"
						},
						{
							label: '胜负过关兑奖',
							type: "31"
						},
						{
							label: '胜负过关退票',
							type: "32"
						},
						{
							label: '参与合买',
							type: "33"
						},
						{
							label: '合买失败退款',
							type: "34"
						},
						{
							label: '合买多余金额退回',
							type: "35"
						},
						{
							label: '合买分奖',
							type: "36"
						},
						{
							label: '选号冠军游戏',
							type: "43"
						},
						{
							label: '冠军游戏兑奖',
							type: "44"
						},
						{
							label: '冠军游戏退票',
							type: "45"
						},
						{
							label: '选号冠亚军游戏',
							type: "46"
						},
						{
							label: '冠亚军游戏兑奖',
							type: "47"
						},
						{
							label: '冠亚军游戏退票',
							type: "48"
						},
						{
							label: '选号福彩3D',
							type: "49"
						},
						{
							label: '福彩3D兑奖',
							type: "50"
						},
						{
							label: '福彩3D退票',
							type: "51"
						},
						{
							label: '选号双色球',
							type: "52"
						},
						{
							label: '双色球兑奖',
							type: "53"
						},
						{
							label: '双色球退票',
							type: "54"
						},
						{
							label: '选号七乐彩',
							type: "55"
						},
						{
							label: '七乐彩兑奖',
							type: "56"
						},
						{
							label: '七乐彩退票',
							type: "57"
						},
						{
							label: '选号快乐8',
							type: "58"
						},
						{
							label: '快乐8兑奖',
							type: "59"
						},
						{
							type: "60",
							label: '快乐8退票',
						},
						{
							type: "64",
							label: '店主加款',
						},{
							type: "65",
							label: '店主扣款',
						}
					]
				],
				orderStatusColumns: [
					[{
						label: '待支付',
						state: "0"
					}, {
						label: '已支付',
						state: "1"
					}, {
						label: '已打款',
						state: "2"
					}]
				],
				queryParam: {
					orderId: "",
					startTime: "",
					endTime: "",
					phone: "",
					state: "",
					type: "",
					pageNo: 1,
					pageSize: 15,
				},
				orderData: [],
				lockParam: {
					password: ""
				},
				delId: "",
				dialogLockVisible: false,
				ishaveData: false,
				scrollTop: 0
			}
		},
		onLoad(option) {
			if (option.item != undefined) {
				let item = JSON.parse(decodeURIComponent(option.item))
				this.queryParam.startTime = item.startTime
				this.queryParam.endTime = item.endTime
				this.queryParam.type = item.type
			}
			this.init();
		},
		onTabItemTap() {
			this.clear();
			this.init();
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
			formatterPayType(payType) {
				if (payType == "0") {
					return "支付宝通道一";
				} else if (payType == "1") {
					return "支付宝通道二";
				} else if (payType == "2") {
					return "支付宝店内加帐";
				} else if (payType == "3") {
					return "微信";
				} else if (payType == "4") {
					return "微信店内加帐";
				} else if (payType == "5") {
					return "APP支付";
				} else if (payType == "6") {
					return "三方支付宝支付";
				} else if (payType == "7") {
					return "三方微信支付";
				}
			},
			formatterState(state) {
				if (state == "0") {
					return "未支付";
				} else if (state == "1") {
					return "已支付";
				} else if (state == "2") {
					return "已打款";
				}
			},
			formatterType(type) {
				if (type == "0") {
					return "账户预存"
				} else if (type == "1") {
					return "选号竞彩足球（扣除）"
				} else if (type == "2") {
					return "选号竞彩篮球（扣除）"
				} else if (type == "3") {
					return "选号北京单场（扣除）"
				} else if (type == "4") {
					return "选号排列3（扣除）"
				} else if (type == "5") {
					return "提现打款"
				} else if (type == "6") {
					return "竞彩足球（兑奖）"
				} else if (type == "7") {
					return "竞彩篮球（兑奖）"
				} else if (type == "8") {
					return "北京单场（兑奖）"
				} else if (type == "9") {
					return "排列3（兑奖）"
				} else if (type == "10") {
					return "竞彩足球（退票）"
				} else if (type == "11") {
					return "竞彩篮球（退票）"
				} else if (type == "12") {
					return "北京单场（退票）"
				} else if (type == "13") {
					return "排列3（退票）"
				} else if (type == "14") {
					return "发单返佣金"
				} else if (type == "15") {
					return "选号排列5"
				} else if (type == "16") {
					return "排列5兑奖"
				} else if (type == "17") {
					return "排列5退票"
				} else if (type == "18") {
					return "选号七星彩"
				} else if (type == "19") {
					return "七星彩兑奖"
				} else if (type == "20") {
					return "七星彩退票"
				} else if (type == "21") {
					return "选号足球14场"
				} else if (type == "22") {
					return "足球14场兑奖"
				} else if (type == "23") {
					return "足球14场退票"
				} else if (type == "24") {
					return "选号任选九"
				} else if (type == "25") {
					return "任选九兑奖"
				} else if (type == "26") {
					return "任选九退票"
				} else if (type == "27") {
					return "选号大乐透"
				} else if (type == "28") {
					return "大乐透兑奖"
				} else if (type == "29") {
					return "大乐透退票"
				} else if (type == "30") {
					return "选号胜负过关"
				} else if (type == "31") {
					return "胜负过关兑奖"
				} else if (type == "32") {
					return "胜负过关退票"
				} else if (type == "33") {
					return "参与合买"
				} else if (type == "34") {
					return "合买失败退款"
				} else if (type == "35") {
					return "合买多余金额退回"
				} else if (type == "36") {
					return "合买分奖"
				} else if (type == "43") {
					return "选号冠军游戏"
				} else if (type == "44") {
					return "冠军游戏兑奖"
				} else if (type == "45") {
					return "冠军游戏退票"
				} else if (type == "46") {
					return "选号冠亚军游戏"
				} else if (type == "47") {
					return "冠亚军游戏兑奖"
				} else if (type == "48") {
					return "冠亚军游戏退票"
				} else if (type == "49") {
					return "选号福彩3D"
				} else if (type == "50") {
					return "福彩3D兑奖"
				} else if (type == "51") {
					return "福彩3D退票"
				} else if (type == "52") {
					return "选号双色球"
				} else if (type == "53") {
					return "双色球兑奖"
				} else if (type == "54") {
					return "双色球退票"
				} else if (type == "55") {
					return "选号七乐彩"
				} else if (type == "56") {
					return "七乐彩兑奖"
				} else if (type == "57") {
					return "七乐彩退票"
				} else if (type == "58") {
					return "选号快乐8"
				} else if (type == "59") {
					return "快乐8兑奖"
				} else if (type == "60") {
					return "快乐8退票"
				}else if (type == "64") {
					return "店主加款"
				}else if (type == "65") {
					return "店主扣款"
				}
			},
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
		methods: {
			backspace() {
				this.lockParam.password = this.lockParam.password.substring(0, this.lockParam.password.length - 1)
			},
			keyboardChange(value) {
				this.lockParam.password += value;
				if (this.lockParam.password.length == 6) {
					this.finish(this.lockParam.password);
				}
			},
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
			finish(val) {
				this.lockParam.password = val;
				checkFlowingWaterPwd(this.lockParam).then(res => {
					if (res.success) {
						delPayOrder(this.delId).then(r => {
							if (r.success) {
								uni.showToast({
									title:"操作成功",
									icon:"success"
								})
								this.dialogLockVisible = false;
								this.init();
							}
						})
					}
				}).catch(e => {
					this.lockParam.password = "";
				})
			},
			delPayOrderHandle(row) {
				this.delId = row.id;
				this.lockParam.password = "";
				this.dialogLockVisible = true;
			},
			orderStatusConfirm(item) {
				this.orderStatusName = item.value[0].label;
				this.queryParam.state = item.value[0].state;
				this.showOrderStatus = false;
			},
			orderTypeConfirm(item) {
				this.orderTypeName = item.value[0].label;
				this.queryParam.type = item.value[0].type;
				this.showOrderType = false;
			},
			hideKeyboard() {
				uni.hideKeyboard()
			},
			init(type) {
				this.ishaveData = false;
				payOrderList(this.queryParam).then((res) => {
					if (type === 'loading') {
						this.orderData = [...this.orderData, ...res.voList]
					} else {
						this.orderData = res.voList == null ? [] : res.voList;
					}
				})
			},
			clear() {
				this.queryParam.state = "";
				this.queryParam.type = "";
				this.queryParam.orderId = "";
				this.queryParam.phone = "";
				this.queryParam.pageNo = 1;
				this.queryParam.startTime = "";
				this.queryParam.endTime = "";
				this.orderStatusName = "";
				this.orderTypeName = "";
				this.dialogLockVisible = false;
				this.init();
			},
		}
	}
</script>


<style scoped lang="scss">
	::v-deep .uni-card__header-extra-text {
		font-size: 14px !important;
	}

	::v-deep .uni-card__content {
		padding: 0px !important;
	}

	::v-deep .uni-card {
		margin: 15px 0px !important;
	}

	::v-deep .u-loadmore {
		padding-bottom: 20px !important;
	}
</style>