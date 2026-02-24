<template>
	<view class="box">
		<!-- 分段条 -->
		<zero-loading v-if="loading" mask></zero-loading>
		<u-back-top :scroll-top="scrollTop" :customStyle="{backgroundColor: '#3999FE'}"
			:iconStyle="{color: '#fff'}"></u-back-top>
		<u-form labelPosition="left" v-model="queryParam" labelWidth="80" style="background: #fff;">
			<u-form-item style="width: 94%;margin: 0 auto;">
				<u-button color="#3999FE" :plain="true" :hairline="true" text="一键兑奖"
					@click="handleAward()"></u-button>
			</u-form-item>
		</u-form>
		<u-empty :show="orderData.length<=0" mode="order" icon="http://cmcpcp.test.upcdn.net/order.png"
			text="暂无订单"></u-empty>
		<view class="container" :style="loading?'width:100%':'width:96%'">
			<view style="display: flex;justify-content: center;align-items: center;" v-for="(item,index) in orderData"
				:key="index">
				<u-checkbox-group shape="square" activeColor="#3999FE" placement="column"
					@change="checkboxChange(item)">
					<u-checkbox :checked="item.check" :key="item.id">
					</u-checkbox>
				</u-checkbox-group>
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
									:style="item.state=='3'||item.state=='4'?'color:#FF3F43':''">{{item.state|formatState}}</span>
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
							<u-button :plain="true" :hairline="true"
								v-if="item.state=='3'&&item.meetShopId==null||item.state=='3'&&item.meetShopId==shop.id"
								color="#3999FE" text="兑奖" @click.native.stop="handleAward(item)"></u-button>
							<u-button :plain="true" :hairline="true"
								v-if="item.state=='1'&&item.meetShopId==null||item.state=='2'&&item.meetShopId==null||item.state=='3'&&item.meetShopId==null"
								color="#3999FE" text="修改兑奖金额" customStyle="margin-left: 10px"
								@click.native.stop="handleMovementAward(item)"></u-button>
							<u-button :plain="true" :hairline="true" color="#3999FE"
								v-if="item.isMeet=='1'&&item.meetShopId==null&&item.state=='0'" text="甩单"
								customStyle="margin-left: 10px" @click.native.stop="sell(item)"></u-button>
						</view>
					</view>
				</uni-card>
			</view>
		</view>
		<template>
			<view class="bottmTab">
				<view style="display: flex;flex: 1;justify-content: space-between;">
					<view style="display: flex;align-items: center;justify-content: center;margin-left: 20upx;">
						<u-checkbox-group shape="square" activeColor="#3999FE" placement="column"
							@change="allCheckboxChange"><u-checkbox>
							</u-checkbox>
						</u-checkbox-group>
						<text>全选</text>
					</view>
					<view>
						<text>合计：{{pitchOrderMoney}}</text>
					</view>
				</view>
				<view class="button-yellow" @click="clearing">
					<text>派奖</text>
				</view>
			</view>
		</template>
		<u-popup :show="updateOrderDialogVisible" @close="updateOrderDialogVisible=false">
			<view style="margin-top: 30px;margin-left: 10px;">
				<u--form labelPosition="left" :model="updateOrderParam" labelWidth="80">
					<u-form-item label="开奖状态" prop="state" borderBottom @click="awardShow = true; hideKeyboard()">
						<u--input v-model="awardName" disabled disabledColor="#ffffff" placeholder="请选择开奖状态"
							border="none"></u--input>
						<u-icon slot="right" name="arrow-right"></u-icon>
					</u-form-item>
					<u-form-item v-if="updateOrderParam.state=='3'" label="修改金额" prop="winPrice" borderBottom>
						<u--input v-model="updateOrderParam.winPrice" border="none"></u--input>
					</u-form-item>
				</u--form>
			</view>
			<view style="display: flex;justify-content: center;align-items: center;margin-bottom: 10px;">
				<u-button text="取消" customStyle="margin: 10px" @click="updateOrderDialogVisible = false"></u-button>
				<u-button color="#3999FE" text="确定" customStyle="margin: 10px" @click="updateOrderBtn"></u-button>
			</view>
			<!-- 手动开奖选择组件 -->
			<u-picker confirmColor="3999FE" :show="awardShow" :columns="awardActions" keyName="name"
				@cancel="awardShow=false" @confirm="awardSelect"></u-picker>
		</u-popup>
		<u-loadmore v-if="ishaveData" :line="true" status="nomore"></u-loadmore>
	</view>
</template>

<script>
	import {
		lotteryOrderList,
		award,
		artificialAward
	} from '@/api/lotteryOrder.js'
	export default {
		data() {
			return {
				awardActions: [
					[{
						name: "未中奖",
						value: "2"
					}, {
						name: "已中奖",
						value: "3"
					}]
				],
				awardShow: false,
				pitchOrderMoney: 0,
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
				orderData: [],
				that: this,
				scrollTop: 0,
				loading: false,
				ishaveData: false,
				awardDisabled: true,
				disable: true,
				checked: false,
				award: {
					id: "",
					ids: [],
				},
				ticketing: {
					state: "",
					id: "",
				},
				updateOrderParam: {
					id: "",
					state: "",
					winPrice: "",
				},
				awardName: "",
				updateOrderDialogVisible: false,

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
		onNavigationBarButtonTap(event) {
			uni.navigateTo({
				url: "/pages/order/orderLnquiry?state=4"
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
			this.init()
		},
		methods: {
			updateOrderBtn() {
				if (this.updateOrderParam.state == "") {
					uni.showToast({
						title: '开奖状态不能为空',
						icon: 'error'
					});
					return;
				}
				if (this.updateOrderParam.state == '3') {
					if (this.updateOrderParam.winPrice == '') {
						uni.showToast({
							title: '兑奖金额不能为空',
							icon: 'error'
						});
						return;
					}
				}
				this.updateOrderDialogVisible = false;
				uni.showModal({
					title: '提示',
					confirmColor: "#3999FE",
					content: '确定进行手动兑奖？如为已中奖,中奖金额请自行计算正确,如若算错,平台不做任何补偿?',
					success: res => {
						if (res.confirm) {
							if (this.disable) {
								this.disable = false;
								artificialAward(this.updateOrderParam).then(res => {
									if (res.success) {
										uni.showToast({
											title: '派奖成功~'
										})
										this.init()
										this.disable = true;
									}
								}).catch(e => {
									this.disable = true;
								})
							}
						}
					}
				});
			},
			hideKeyboard() {
				uni.hideKeyboard()
			},
			awardSelect(e) {
				this.awardName = e.value[0].name;
				this.updateOrderParam.state = e.value[0].value
				this.awardShow = false;
			},
			handleMovementAward(item) {
				this.updateOrderParam.id = item.id;
				this.updateOrderParam.state = "";
				this.awardName = "";
				this.updateOrderParam.winPrice = "";
				this.updateOrderDialogVisible = true;
			},
			totalBtn() {
				this.pitchOrderMoney = 0;
				this.award.ids = [];
				this.orderData.map(item => {
					if (item.check) {
						this.award.ids.push(item.id)
						this.pitchOrderMoney = (parseFloat(this.pitchOrderMoney) + parseFloat(item.winPrice))
							.toFixed(2)
					}
				})
			},
			handleAward(item) {
				if (this.awardDisabled) {
					this.awardDisabled = false;
					if (item != undefined) {
						this.award.id = item.id;
						uni.showModal({
							title: '提示',
							confirmColor: "#3999FE",
							content: '确定兑奖?',
							success: res => {
								if (res.confirm) {
									if (this.disable) {
										this.disable = false;
										award(this.award).then(res => {
											if (res.success) {
												uni.showToast({
													title: "操作成功",
													icon: "success"
												})
												this.init()
												this.awardDisabled = true;
												this.disable = true;
											}
										}).catch(error => {
											this.awardDisabled = true;
											this.disable = true;
										})
									}
								}
							}
						});
					} else {
						uni.showModal({
							title: '提示',
							confirmColor: "#3999FE",
							content: '确定一键兑奖?',
							success: res => {
								if (res.confirm) {
									if (this.disable) {
										this.disable = false;
										award(this.ticketing).then(res => {
											if (res.success) {
												uni.showToast({
													title: "操作成功",
													icon: "success"
												})
												this.init()
												this.awardDisabled = true;
												this.disable = true;
											}
										}).catch(() => {
											this.awardDisabled = true;
											this.disable = true;
										});
									}
								} else {
									this.awardDisabled = true;
								}
							}
						});
					}
				}
			},
			clearing() {
				if (this.award.ids.length > 0) {
					uni.showLoading({
						title: '派奖中~'
					})
					award(this.award).then(res => {
						if (res.success) {
							uni.hideLoading()
							uni.showToast({
								title: '派奖成功~'
							})
							this.award.ids.forEach((id) => {
								const index = this.orderData.findIndex(item => item.id === id);
								if (index !== -1) {
									console.log(index)
									this.orderData.splice(index, 1)
								}
							})
							this.totalBtn()
						}
					})
				} else {
					uni.showToast({
						title: '请选择派奖订单~',
						icon: 'error'
					})
				}
			},
			allCheckboxChange() {
				this.checked = !this.checked;
				this.orderData.map(item => {
					if (this.checked) {
						item.check = true;
					} else {
						item.check = false;
					}
				})
				this.totalBtn()
			},
			checkboxChange(e) {
				e.check = !e.check;
				this.totalBtn()
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

	.button-yellow {
		display: flex;
		background-color: #3999FE;
		width: 270rpx;
		height: 80rpx;
		align-items: center;
		justify-content: center;
		margin-left: 20rpx;
		color: white;
		font-weight: 600;
		border-radius: 20rpx 0 0 20rpx;
	}

	.bottmTab {
		z-index: 999;
		width: 100%;
		height: 120rpx;
		background-color: white;
		box-shadow: #c1c1c1 10rpx 10rpx 30rpx 1rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		position: fixed;
		bottom: 0px;
		left: 0px;
	}

	page {
		background-color: #f3f3f3;
	}
</style>