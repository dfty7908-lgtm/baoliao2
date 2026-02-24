<template>
	<view style="width: 94%;margin: 0 auto;">
		<view style="margin-top: 5px;">
			<u-tag text="点击如下滚动文字即可修改" plain size="mini" type="warning" style="margin-bottom: 5px;"></u-tag>
			<u-notice-bar v-if="noticeData[0]!=undefined" :text="noticeData[0].msg" color="#ffffff" bgColor="#3999FE"
				@click="handleUpdate(noticeData[0])"></u-notice-bar>
			<u-button color="#3999FE" text="推送" customStyle="margin: 10px 0px" @click="push"></u-button>
		</view>
		<u-popup :show="dialogVisible" @close="dialogVisible=false">
			<view
				style="display: flex;justify-content: flex-start;align-items: center;margin-top: 30px;margin-left: 10px;margin-right: 10px;">
				<u-form :rules="noticeRule" labelPosition="left" ref="ruleForm" :model="noticeParam" labelWidth="80"
					style="width: 100%;">
					<u-form-item label="公告内容" borderBottom prop="msg">
						<u-textarea maxlength="-1" v-model="noticeParam.msg" autoHeight border="none"
							count></u-textarea>
					</u-form-item>
				</u-form>
			</view>
			<view style="display: flex;justify-content: center;align-items: center;margin-bottom: 10px;">
				<u-button text="取消" customStyle="margin: 10px" @click="dialogVisible=false"></u-button>
				<u-button color="#3999FE" text="确定" customStyle="margin: 10px" @click="confirm"></u-button>
			</view>
		</u-popup>
	</view>
</template>

<script>
	import {
		noticeList,
		noticeUpdate,
		updateRead
	} from '@/api/notice.js'
	export default {
		data() {
			return {
				noticeData: [],
				dialogVisible: false,
				noticeParam: {
					msg: "",
					id: "",
				},
				noticeRule: {
					msg: [{
						required: true,
						message: '公告不能为空',
						trigger: ["blur", "change"]
					}, ],
				},
			}
		},
		onLoad(option) {
			this.init();
		},
		methods: {
			push() {
				updateRead(this.noticeData[0].id).then(res => {
					if(res.success){
						uni.$u.toast('推送成功')
					}
				})
			},
			init() {
				noticeList().then((res) => {
					this.noticeData = res.voList
				})
			},
			handleUpdate(item) {
				this.noticeParam.msg = item.msg;
				this.noticeParam.id = item.id;
				this.dialogVisible = true;
			},
			confirm() {
				this.$refs.ruleForm.validate().then(res => {
					noticeUpdate(this.noticeParam.id, this.noticeParam).then(res => {
						if (res.success) {
							uni.showToast({
								title:"设置成功",
								icon:"success"
							})
							this.dialogVisible = false;
							this.init();
						}
					})
				}).catch(errors => {
					uni.$u.toast('请填写完整')
				})
			}
		}
	}
</script>


<style scoped lang="scss">
</style>