<template>
	<view class="box">
		<view class="tita">
			<text>基本信息</text>
		</view>
		<view style="width:100%;background-color: white;">
			<!-- <view class="dataCard" @click="that.$refs.identity.open()">
				<text>实名认证</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;"
						v-if="user.adminShop!=undefined">{{user.adminShop.state!='2'?'未认证':'已认证'}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view> -->
			<view class="dataCard">
				<text>用户名</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;" v-if="user.adminShop!=undefined">{{user.username}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="dataCard" @click="updateBtn(1)">
				<text>绑定手机</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;" v-if="user.adminShop!=undefined">{{user.phone}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="dataCard" @click="updateBtn(0)">
				<text>支付密码</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;" v-if="user.adminShop!=undefined">******</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="dataCard" @click="btn('/pages/email/email')">
				<text>推送通知</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;" v-if="user.adminShop!=undefined">{{user.email}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
		</view>
		<view class="tita">
			<text>店铺信息</text>
		</view>
		<view style="width:100%;background-color: white;">
			<view class="dataCard" @click="that.$refs.logo.open()">
				<text>店铺头像</text>
				<image v-if="user.adminShop!=undefined" :src="user.adminShop.logo"
					style="width:80rpx; height:80rpx;border-radius:10rpx;" mode="aspectFill">
				</image>
			</view>
			<view class="dataCard" @click="nicknameBtn">
				<text>店铺昵称</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;" v-if="user.adminShop!=undefined">{{user.adminShop.name}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="dataCard" @click="btn('/pages/shop/authentication')">
				<text>店铺认证</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;"
						v-if="user.adminShop!=undefined">{{user.adminShop.state!='2'?'未认证':'已认证'}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="dataCard" @click="that.$refs.citys.open()">
				<text>店铺地址</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;"
						v-if="user.adminShop!=undefined">{{user.adminShop.address==null?'未设置':user.adminShop.address}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="dataCard" @click="that.$refs.vxSet.open()">
				<text>店铺微信</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;"
						v-if="user.adminShop!=undefined">{{user.adminShop.vx==null?'未设置':user.adminShop.vx}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="dataCard" @click="that.$refs.phoneSet.open()">
				<text>店铺电话</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<text style="margin-right:10rpx;"
						v-if="user.adminShop!=undefined">{{user.adminShop.phone==null?'未设置':user.adminShop.phone}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
		</view>

		<view class="tita">
			<text>店铺设置</text>
		</view>
		<view style="width:100%;background-color: white;height: 450upx;">
			<view class="dataCard" @click="setPayDialogVisible=true">
				<text>预存设置</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="dataCard" @click="setDialogVisible=true">
				<text>提现设置</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="dataCard" @click="setNitoDialogVisible=true">
				<text>首页中奖滚动提醒设置</text>
				<view style="font-size:22rpx;color: #555555;display: flex;">
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
		</view>

		<view class="bottomTab" @click="logout()">
			<text>退出登录/切换账号</text>
		</view>

		<u-popup :show="setNitoDialogVisible" @close="setNitoDialogVisible=false" @open="setOpen">
			<h4 style="text-align: center;padding-top: 30upx;">首页中奖滚动提醒设置</h4>
			<view style="display: flex;align-items: center;justify-content: space-between;margin: 30px">
				<view style="display: flex;flex-direction: column;">
					<text style="font-size: 16px;">首页中奖滚动提醒</text>
					<text style="font-size: 12px;color: #9f9f9f;">关闭后，客户端首页滚动中奖的提醒将被隐藏</text>
				</view>
				<view>
					<u-switch v-model="switchFlag2" activeColor="#3999FE"
						@change="withdrawalChange(dictData5,$event)"></u-switch>
				</view>
			</view>
		</u-popup>

		<uni-popup ref="nicknameSet" type="dialog">
			<uni-popup-dialog title='请输入您要修改的店铺昵称' mode='input' :value="updateShopParam.name" @confirm="setNickName">
			</uni-popup-dialog>
		</uni-popup>
		<uni-popup ref="vxSet" type="dialog">
			<uni-popup-dialog title='请输入您要修改的微信号' mode='input' :value="updateShopParam.vx" @confirm="setVx">
			</uni-popup-dialog>
		</uni-popup>
		<uni-popup ref="phoneSet" type="dialog">
			<uni-popup-dialog title='请输入您要修改的电话' mode='input' :value="updateShopParam.phone" @confirm="setPhone">
			</uni-popup-dialog>
		</uni-popup>

		<uni-popup ref="phonteSet" type="center">
			<view class="setCard">
				<uni-forms label-position="top" :rules="formsRules" v-model="updateUserParam" ref="forms">
					<uni-forms-item label="手机" required name="phone">
						<uni-easyinput placeholder="请输入手机号" v-model="updateUserParam.phone" type="number" trim="all" />
					</uni-forms-item>
					<uni-forms-item label="验证码" required name="code">
						<view class="codeBox">
							<uni-easyinput placeholder="请输入验证码" maxlength="6" v-model="updateUserParam.code"
								trim="all" />
							<view @click="gainCode"
								:class="typeof(codeNumber)=='string' &&  isPhone(updateUserParam.phone)?'codeButton':'codeButtonFalse'">
								{{codeNumber}}
							</view>
						</view>
					</uni-forms-item>
					<uni-forms-item label="支付密码" v-if="type==0">
						<uni-easyinput placeholder="请输入支付密码" maxlength="6" v-model="updateUserParam.payPwd"
							type="number" trim="all" />
					</uni-forms-item>
				</uni-forms>

				<view class="buttomStyle" @click="allData">提交</view>
			</view>
		</uni-popup>

		<uni-popup type="bottom" ref="logo">
			<view style="width:100%;border-radius:20rpx 20rpx 0 0;">
				<view class="logoButton" v-for="itme,index in 3" :key="index" @click="amendLogo(index)"
					:style="index==2?'margin-top:20rpx;':''">
					<text>{{index==0?'查看头像':index==1?'修改头像':'取消'}}</text>
				</view>
			</view>
		</uni-popup>

		<uni-popup ref="citys" type="center" :isMaskClick="false">
			<view class="citysPopupCard">
				<text style="font-weight: bold;">设置店铺地址</text>
				<view style="text-align: left;">
					<text style="padding-bottom:20rpx;">选择地区：</text>
					<uni-data-picker v-if="citysData!=undefined" popupTitle="请选择城市" :localdata="citysData.citys"
						@change="change" />
				</view>
				<view style="margin:20rpx 0rpx;text-align: left;">
					<text>详细地址：</text>
					<uni-easyinput placeholder="请输入详细地址" v-model="detailedAddress" />
				</view>
				<view style="display: flex;justify-content: space-between;">
					<view class="citysButtom" style="background-color:#afafaf;" @click="that.$refs.citys.close()">
						<text>取消</text>
					</view>
					<view class="citysButtom" style="background-color:#3999FE;" @click="confirm">
						<text>确定</text>
					</view>
				</view>
			</view>
		</uni-popup>
		<uni-popup ref="identity" type="center" :isMaskClick="false">
			<view class="citysPopupCard">
				<text style="font-weight: bold;">实名认证</text>
				<view style="text-align: left;">
					<text>身份证号：</text>
					<uni-easyinput placeholder="请输入您身份证号码" type="number" maxlength="18" v-if="1==1" />
					<text v-else>65161</text>
				</view>
				<view style="text-align: left;margin:20rpx 0rpx;">
					<text>真实姓名：</text>
					<uni-easyinput placeholder="请输入您真实姓名" v-if="1==1" />
					<text v-else>65161</text>
				</view>
				<view style="display: flex;justify-content: space-between;">
					<view class="citysButtom" style="background-color:#afafaf;" @click="that.$refs.identity.close()">
						<text>取消</text>
					</view>
					<view class="citysButtom" style="background-color:#3999FE;" @click="identityConfirm">
						<text>确定</text>
					</view>
				</view>
			</view>
		</uni-popup>
		<u-popup :show="setDialogVisible" @close="setDialogVisible=false" @open="setOpen">
			<h4 style="text-align: center;padding-top: 30upx;">提现设置</h4>
			<view style="display: flex;align-items: center;justify-content: space-between;margin: 30px">
				<view style="display: flex;flex-direction: column;">
					<text style="font-size: 16px;">提现开关</text>
					<text style="font-size: 12px;color: #9f9f9f;">关闭后，客户端提现入口隐藏</text>
				</view>
				<view>
					<u-switch v-model="switchFlag1" activeColor="#3999FE"
						@change="withdrawalChange(dictData2,$event)"></u-switch>
				</view>
			</view>
			<view style="display: flex;align-items: center;justify-content: space-between;margin: 0px 30px 30px">
				<view style="display: flex;flex-direction: column;">
					<text style="font-size: 16px;">最低提现金额</text>
					<text style="font-size: 12px;color: #9f9f9f;">设置客户端最低提现金额</text>
				</view>
				<view style="display: flex;align-items: center;justify-content: center;">
					<u-number-box v-model="dictData4.value" min="2" integer
						@change="numberChange(dictData4,$event)"></u-number-box>
					<span style="padding-left: 10upx;">元</span>
				</view>
			</view>
			<view style="display: flex;align-items: center;justify-content: space-between;margin: 0px 30px 30px">
				<view style="display: flex;flex-direction: column;">
					<text style="font-size: 16px;">提现时间</text>
					<text style="font-size: 12px;color: #9f9f9f;">(10,15)含义是早上10点到下午15点为提现时间</text>
				</view>
				<view @click="timeShow = true; hideKeyboard()" style="margin-left: 50px;">
					<u--input v-model="dictData3.value" border="none"></u--input>
				</view>
			</view>
		</u-popup>
		<u-picker confirmColor="#3999FE" :show="timeShow" :columns="timeColumns" @cancel="timeShow=false"
			@confirm="timeSelect(dictData3,$event)"></u-picker>


		<u-popup :show="setPayDialogVisible" @close="setPayDialogVisible=false" @open="setOpen">
			<h4 style="text-align: center;padding-top: 30upx;">预存设置</h4>
			<view style="display: flex;align-items: center;justify-content: space-between;margin: 30px">
				<view style="display: flex;flex-direction: column;">
					<text style="font-size: 16px;">预存开关</text>
					<text style="font-size: 12px;color: #9f9f9f;">关闭后，客户端充值入口隐藏</text>
				</view>
				<view>
					<u-switch v-model="switchFlag" activeColor="#3999FE"
						@change="withdrawalChange(dictData,$event)"></u-switch>
				</view>
			</view>
			<view style="display: flex;align-items: center;justify-content: space-between;margin: 0px 30px 30px">
				<view style="display: flex;flex-direction: column;">
					<text style="font-size: 16px;">最低预存金额</text>
					<text style="font-size: 12px;color: #9f9f9f;">设置客户端最低预存金额</text>
				</view>
				<view>
					<u-number-box v-model="dictData1.value" min="1" integer
						@change="numberChange(dictData1,$event)"></u-number-box>
				</view>
			</view>
		</u-popup>
	</view>
</template>

<script>
	import cfg from '@/util/environment.js';
	import citysData from "@/api/citys.js"
	import {
		updateShop
	} from '@/api/shop.js'
	import {
		removeAuthtoken,
		removeUserInfo,
	} from '@/util/auth.js'
	import {
		logout,
		getUser,
		send
	} from '@/api/user.js'
	import {
		updateUser
	} from '@/api/sysUser.js'
	import {
		getDictList,
		putDict
	} from '@/api/dict.js'
	export default {
		data() {
			return {
				setPayDialogVisible: false,
				codeParam: {
					codeList: ["payment", "recharge_price", "withdrawal", "withdrawal_data_time", "minimum_withdrawal",
						"win_remind"
					]
				},
				timeColumns: [
					["0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12", "13", "14", "15", "16", "17",
						"18", "19", "20", "21", "22", "23"
					],
					["0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12", "13", "14", "15", "16", "17",
						"18", "19", "20", "21", "22", "23"
					],
				],
				timeShow: false,
				switchFlag: false,
				switchFlag1: false,
				switchFlag2: false,
				dictData: {},
				dictData1: {},
				dictData2: {},
				dictData3: {},
				dictData4: {},
				dictData5: {},
				putParam: {
					id: "",
					value: ""
				},
				setDialogVisible: false,
				type: "",
				codeNumber: '获取验证码',
				that: this,
				user: {},
				setNitoDialogVisible: false,
				setTime: null,
				updateShopParam: {
					id: null,
					name: null,
					logo: null,
					address: null,
					vx: null,
					phone: null
				},
				citysData: citysData,
				updateUserParam: {
					id: "",
					phone: null,
					code: null,
					username: null,
					payPwd: null,
				},
				detailedAddress: "",
				formsRules: {
					phone: {
						rules: [{
								required: true,
								errorMessage: '手机号不能为空'
							},
							{
								validateFunction: function(rule, value, data, callback) { //手机号码校验
									var res = /^[1][3,4,5,6,7,8,9][0-9]{9}$/;
									if (!res.test(value)) {
										callback('请输入正确的手机号!')
									} else {
										callback()
									}
									return true
								}
							}
						]
					},
					code: {
						rules: [{
							required: true,
							errorMessage: '验证码不能为空'
						}, ]
					},
				}
			}
		},
		onLoad() {
			this.init()
		},
		methods: {
			hideKeyboard() {
				uni.hideKeyboard()
			},
			withdrawalChange(item, flag) {
				this.putParam = item;
				if (flag) {
					this.putParam.value = "0";
				} else {
					this.putParam.value = "1";
				}
				this.updateDict();
			},
			numberChange(item, d) {
				this.putParam = item;
				this.putParam.value = d.value;
				this.updateDict();
			},
			updateDict() {
				putDict(this.putParam).then(res => {
					if (res.success) {
						uni.showToast({
							title: "设定成功~",
							icon: "success"
						})
						this.setOpen()
					}
				})
			},
			timeSelect(item, d) {
				this.putParam = item;
				this.putParam.value = d.value[0] + "," + d.value[1]
				this.updateDict();
				this.timeShow = false;
			},
			setOpen() {
				this.putParam.id = "";
				this.putParam.value = "";
				getDictList(this.codeParam).then(res => {
					this.dictData = res.voList[0];
					this.switchFlag = this.dictData.value == "0" ? true : false;
					this.dictData1 = res.voList[1];
					this.dictData2 = res.voList[2];
					this.switchFlag1 = this.dictData2.value == "0" ? true : false;
					this.dictData3 = res.voList[3];
					this.dictData4 = res.voList[4];
					this.dictData5 = res.voList[5];
					this.switchFlag2 = this.dictData5.value == "0" ? true : false;
				})
			},
			change(e) {
				this.updateShopParam.address = ''
				for (var i = 0; i < e.detail.value.length; i++) {
					this.updateShopParam.address += e.detail.value[i].text
				}
			},
			confirm() { //设置店铺地址
				this.updateShopParam.id = this.user.adminShop.id
				if (this.updateShopParam.address != '' && this.detailedAddress != '') {
					this.updateShopParam.address = this.updateShopParam.address + this.detailedAddress
					uni.showLoading({
						title: '设置中~'
					})
					updateShop(this.updateShopParam).then(res => {
						if (res.success) {
							uni.hideLoading()
							uni.showToast({
								title: '设置成功~'
							})
							this.$refs.citys.close()
							this.init()
						}
					})
				} else {
					uni.showToast({
						title: '地址不能为空~',
						icon: 'error',
					})
				}

			},
			nicknameBtn() {
				this.updateShopParam.name = this.user.adminShop.name
				this.$refs.nicknameSet.open()
			},
			setNickName(e) {
				this.updateShopParam.id = this.user.adminShop.id
				this.updateShopParam.name = e
				updateShop(this.updateShopParam).then(res => {
					uni.showToast({
						title: '操作成功',
						icon: "success"
					})
				})
				this.init();
				this.$refs.nicknameSet.close()
			},
			setVx(e) {
				this.updateShopParam.id = this.user.adminShop.id
				this.updateShopParam.vx = e
				updateShop(this.updateShopParam).then(res => {
					uni.showToast({
						title: '操作成功',
						icon: "success"
					})
				})
				this.init();
				this.$refs.vxSet.close()
			},
			setPhone(e) {
				this.updateShopParam.id = this.user.adminShop.id
				this.updateShopParam.phone = e
				updateShop(this.updateShopParam).then(res => {
					uni.showToast({
						title: '操作成功',
						icon: "success"
					})
				})
				this.init();
				this.$refs.phoneSet.close()
			},
			updateBtn(type) {
				this.type = type
				this.$refs.phonteSet.open()
			},
			clear() {
				this.updateUserParam.phone = null
				this.updateUserParam.code = null
				this.updateUserParam.payPwd = null
				this.updateShopParam.address = null
				this.updateShopParam.vx = null
				this.updateShopParam.phone = null
				this.updateShopParam.name = null;
				this.updateShopParam.logo = null;
			},
			init() {
				getUser().then(res => {
					this.user = res;
				})
			},
			allData() { //提交绑定
				this.$refs.forms.validate().then(res => {
					if (this.type == 0) {
						if (this.updateUserParam.payPwd == "") {
							uni.showLoading({
								title: '支付密码不能为空',
								mask: true,
								icon: "none"
							})
							return;
						}
					}
					uni.showLoading({
						title: '绑定中',
						mask: true
					})
					this.updateUserParam.id = this.user.id
					this.updateUserParam.username = this.user.username
					updateUser(this.updateUserParam).then(res => {
						if (res.success) {
							uni.showToast({
								title: '操作成功',
								icon: 'success',
							})
						}
						this.$refs.phonteSet.close()
						this.init();
						this.clear()
					}).catch(err => {
						uni.hideLoading()
					})
				})
			},
			isPhone(phone) {
				var myreg = /^[1][3,4,5,6,7,8,9][0-9]{9}$/;
				if (!myreg.test(phone)) {
					return false;
				} else {
					return true;
				}
			},
			gainCode() {
				if (this.updateUserParam.phone == "") {
					uni.showToast({
						title: '请填写手机号',
						icon: "error",
					})
				}
				uni.showLoading({
					title: '发送中',
					mask: true
				})
				send(this.updateUserParam).then(res => {
					if (res.success) {
						uni.showToast({
							title: '已发送验证码~',
							position: 'center',
							icon: 'none'
						}) //验证码按钮点击事件
						this.codeNumber = 60
						this.setTime = setInterval(() => {
							this.codeNumber = this.codeNumber - 1
							if (this.codeNumber == 0) {
								clearInterval(this.setTime)
								this.setTime = null
								this.codeNumber = '获取验证码'
							}
						}, 1000)
					}
				})
			},
			btn(url) {
				uni.navigateTo({
					url
				})
			},
			amendLogo(index) {
				if (index == 0) {
					uni.previewImage({
						current: 0,
						urls: [this.user.adminShop.logo]
					});
				} else if (index == 1) {
					uni.chooseImage({
						count: 1,
						crop: {
							width: 250,
							height: 250,
							quality: 90
						},
						success: (Res) => {
							uni.showLoading({
								title: '上传中~'
							})
							var imgUrl = Res.tempFilePaths[0]
							uni.uploadFile({
								url: cfg.BASE_API + "/file/sys/upload",
								header: {
									'x-access-token': uni.getStorageSync("vue_authtoken"),
									"x-sys": "1",
									'x-user': uni.getStorageSync("x-user"),
								},
								filePath: imgUrl,
								name: 'file',
								success: (res) => {
									let resData = JSON.parse(res.data);
									if (resData.success) {
										//修改头像接口
										this.updateShopParam.id = this.user.adminShop.id
										this.updateShopParam.logo = resData.url
										updateShop(this.updateShopParam).then(res => {
											uni.showToast({
												title: '操作成功',
												icon: "success"
											})
										})
										uni.hideLoading()
									} else {
										uni.$u.toast(resData.errorMsg)
										resolve("")
									}
								}
							})
						}
					})
					this.$refs.logo.close()
				} else {
					this.$refs.logo.close()
				}
			},
			logout() {
				uni.showModal({
					title: '提示',
					confirmColor: "#3999FE",
					content: '确定退出登录吗?',
					success: res => {
						if (res.confirm) {
							uni.showLoading();
							logout().then(res => {
								if (res.success) {
									removeAuthtoken();
									removeUserInfo();
									uni.removeStorageSync("x-user")
									uni.removeStorageSync("shop")
									uni.redirectTo({
										url: "/pages/login/login"
									});
									uni.clearStorageSync()
									uni.hideLoading();
								}
							})
						}
					}
				});
			},
		},
	}
</script>

<style scoped>
	.buttomStyle {
		width: 100%;
		height: 80rpx;
		background-color: #3999FE;
		border-radius: 15rpx;
		text-align: center;
		line-height: 80rpx;
		color: white;
	}

	.setCard {
		width: 680rpx;
		padding: 20rpx;
		border-radius: 20rpx;
		background-color: #fff;

		.title_box {
			font-size: 38rpx;
			font-weight: bold;
			text-align: center;
			margin-bottom: 20rpx;
		}
	}

	.codeBox {
		display: flex;
		align-items: center;

		.codeButton {
			background-color: #3999FE;
			color: white;
			width: 200rpx;
			margin-left: 10rpx;
			height: 70rpx;
			text-align: center;
			line-height: 70rpx;
			border-radius: 10rpx;
			transition: .3s;
		}

		.codeButtonFalse {
			background-color: #cccccc;
			color: #efefef;
			width: 200rpx;
			margin-left: 10rpx;
			height: 70rpx;
			text-align: center;
			line-height: 70rpx;
			border-radius: 10rpx;
			transition: .3s;
		}
	}

	.citysButtom {
		width: 260rpx;
		height: 60rpx;
		border-radius: 10rpx;
		color: white;
		text-align: center;
		line-height: 60rpx;
	}

	.citysPopupCard {
		width: 640rpx;
		padding: 30rpx;
		background-color: white;
		border-radius: 30rpx;
		text-align: center;
	}

	.logoButton {
		width: 100%;
		height: 80rpx;
		text-align: center;
		line-height: 80rpx;
		border-bottom: 1rpx #eeeeee solid;
		background-color: white;
	}

	.popupStyle {
		width: 700rpx;
		height: 400rpx;
		background-color: white;
		display: flex;
		align-items: center;
		flex-flow: column;
		border-radius: 20rpx;
	}

	.bottomTab {
		width: 100%;
		height: 90rpx;
		background-color: white;
		position: fixed;
		bottom: 0rpx;
		color: #3999FE;
		display: flex;
		align-items: center;
		justify-content: center;
		border-radius: 20rpx 20rpx 0 0;
	}

	.tita {
		height: 65rpx;
		display: flex;
		align-items: center;
		margin-left: 20rpx;

	}

	.tita text {
		font-size: 32rpx;
		font-weight: bold;
	}

	.dataCard {
		width: 710rpx;
		height: 95rpx;
		margin: auto;
		display: flex;
		align-items: center;
		justify-content: space-between;
		border-bottom: 1rpx solid #f3f3f3;
	}

	page {
		background-color: #f3f3f3;
	}
</style>