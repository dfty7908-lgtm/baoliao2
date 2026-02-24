<template>
	<view>
		<u-sticky bgColor="#fff">
			<u-subsection style="height: 40px;" :list="lassificationcList" inactiveColor="#3999FE" fontSize="16"
				:current="selectIndex" activeColor="#3999FE" @change="changeSelectBall"></u-subsection>
		</u-sticky>
		<u-form labelPosition="left" v-model="balanceParam" labelWidth="80" style="width: 96%;margin: 0 auto;">
			<u-form-item label="起始时间" borderBottom>
				<uni-datetime-picker type="datetime" @change="startChange" v-model="balanceParam.startTime" />
			</u-form-item>
			<u-form-item label="结束时间" borderBottom>
				<uni-datetime-picker type="datetime" @change="endChange" v-model="balanceParam.endTime" />
			</u-form-item>
			<u-form-item>
				<u-button color="#3999FE" text="查询" @click="query()"></u-button>
				<u-button color="#3999FE" text="重置选择" customStyle="margin-left: 10px" @click="clear"></u-button>
			</u-form-item>
		</u-form>
		<u-empty :show="selectIndex==0&&balanceList.length<=0" mode="data" icon="http://cmcpcp.test.upcdn.net/data.png"
			text="暂无数据"></u-empty>
		<u-empty :show="selectIndex==1&&orderList.length<=0" mode="data" icon="http://cmcpcp.test.upcdn.net/data.png"
			text="暂无数据"></u-empty>
		<u-empty :show="selectIndex==2&&withdrawalList.length<=0" mode="data"
			icon="http://cmcpcp.test.upcdn.net/data.png" text="暂无数据"></u-empty>
		<u-cell-group v-if="selectIndex==0&&balanceList.length>0">
			<u-cell :iconStyle="{width:'50px',height:'50px',borderRadius:'50%'}" size="large"
				@click="btnWallet(item.phone,item.id)" v-for="(item,index) in balanceList" :key="index"
				:icon="item.avatar" :title="item.nickname" :label="item.createTime|formatDate(that)"
				:value="'总 '+parseFloat(item.gold+item.price).toFixed(2)+' 元'"></u-cell>
		</u-cell-group>
		<u-cell-group v-if="selectIndex==1&&orderList.length>0">
			<u-cell :iconStyle="{width:'50px',height:'50px',borderRadius:'50%'}" size="large"
				@click="btnWallet(item.phone,item.userId)" v-for="(item,index) in orderList" :key="index"
				:title="'系统 | 店主加款-'+item.nickname" :label="item.createTime|formatDate(that)"
				:value="'+'+item.price"></u-cell>
		</u-cell-group>
		<u-cell-group v-if="selectIndex==2&&withdrawalList.length>0">
			<u-cell :iconStyle="{width:'50px',height:'50px',borderRadius:'50%'}" size="large"
				@click="btnWallet(item.phone,item.userId)" v-for="(item,index) in withdrawalList" :key="index"
				:title="'系统 | 店主扣款-'+item.nickname" :label="item.createTime|formatDate(that)"
				:value="'-'+item.price"></u-cell>
		</u-cell-group>
		<u-loadmore v-if="ishaveData" :line="true" status="nomore"></u-loadmore>
	</view>
</template>

<script>
	import {
		userList,
	} from '@/api/user.js'
	import {
		payOrderList,
	} from '@/api/pay.js'
	import {
		withdrawalList,
	} from '@/api/withdrawal.js'
	export default {
		data() {
			return {
				that: this,
				ishaveData: false,
				balanceList: [],
				selectIndex: 0,
				balanceParam: {
					startTime: "",
					endTime: "",
					pageNo: 1,
					pageSize: 20
				},
				orderList: [],
				withdrawalList: [],
				orderParam: {
					startTime: "",
					endTime: "",
					state: "1",
					type: "64",
					pageNo: 1,
					pageSize: 20
				},
				withdrawaParam: {
					startTime: "",
					endTime: "",
					state: "1",
					type: "65",
					pageNo: 1,
					pageSize: 20
				},
				lassificationcList: [{
					name: '余额'
				}, {
					name: '加款'
				}, {
					name: '扣款'
				}],
			}
		},
		//滚动到底部进行分页事件
		onReachBottom() {
			if (this.selectIndex == 0) {
				if (this.balanceList.length < this.balanceParam.pageNo * 10) {
					this.ishaveData = true;
					return;
				}
				this.balanceParam.pageNo++;
				this.init("loading");
			} else if (this.selectIndex == 1) {
				if (this.orderList.length < this.orderParam.pageNo * 10) {
					this.ishaveData = true;
					return;
				}
				this.orderParam.pageNo++;
				this.init("loading");
			} else if (this.selectIndex == 2) {
				if (this.withdrawalList.length < this.withdrawaParam.pageNo * 10) {
					this.ishaveData = true;
					return;
				}
				this.withdrawaParam.pageNo++;
				this.init("loading");
			}
		},
		onPullDownRefresh() {
			if (this.selectIndex == 0) {
				this.balanceList = []
				this.balanceParam.pageNo = 1;
				this.init();
				uni.stopPullDownRefresh()
			} else if (this.selectIndex == 1) {
				this.orderList = []
				this.orderParam.pageNo = 1;
				this.init();
				uni.stopPullDownRefresh()
			} else if (this.selectIndex == 2) {
				this.withdrawalList = []
				this.withdrawaParam.pageNo = 1;
				this.init();
				uni.stopPullDownRefresh()
			}
		},
		filters: {
			formatDate(data, that) {
				if (data != null && data != undefined) {
					return that.globalUtil.dateTimeFormat(data)
				}
			},
		},
		methods: {
			clear() {
				this.balanceParam.startTime = "";
				this.balanceParam.endTime = "";
				this.balanceParam.pageNo = 1;
				this.orderParam.pageNo = 1;
				this.withdrawaParam.pageNo = 1;
				this.init()
			},
			query() {
				this.balanceParam.pageNo = 1;
				this.init();
			},
			startChange(e) {
				this.balanceParam.startTime = new Date(e);
			},
			endChange(e) {
				this.balanceParam.endTime = new Date(e);
			},
			btnWallet(phone, userId) {
				uni.navigateTo({
					url: "/pages/user/wallet?phone=" + phone + "&id=" + userId
				})
			},
			changeSelectBall(index) {
				this.selectIndex = index
				this.balanceParam.pageNo = 1;
				this.orderParam.pageNo = 1;
				this.withdrawaParam.pageNo = 1;
				this.init();
			},
			init(type) {
				this.ishaveData = false;
				if (this.selectIndex == 0) {
					userList(this.balanceParam).then(res => {
						if (type === 'loading') {
							this.balanceList = [...this.balanceList, ...res.voList]
						} else {
							this.balanceList = res.voList == null ? [] : res.voList;
						}
					})
				} else if (this.selectIndex == 1) {
					this.orderParam.startTime = this.balanceParam.startTime
					this.orderParam.endTime = this.balanceParam.endTime
					payOrderList(this.orderParam).then(res => {
						if (type === 'loading') {
							this.orderList = [...this.orderList, ...res.voList]
						} else {
							this.orderList = res.voList == null ? [] : res.voList;
						}
					})
				} else if (this.selectIndex == 2) {
					this.withdrawaParam.startTime = this.balanceParam.startTime
					this.withdrawaParam.endTime = this.balanceParam.endTime
					payOrderList(this.withdrawaParam).then(res => {
						if (type === 'loading') {
							this.withdrawalList = [...this.withdrawalList, ...res.voList]
						} else {
							this.withdrawalList = res.voList == null ? [] : res.voList;
						}
					})
				}
			},
		},
		onLoad(option) {
			if (option.selectIndex != undefined) {
				this.selectIndex = option.selectIndex
			}
			this.init();

		}
	}
</script>

<style scoped lang="scss">
	::v-deep .u-cell__value {
		color: #3999FE;
	}

	::v-deep .u-loadmore {
		padding-bottom: 40upx !important;
	}
</style>