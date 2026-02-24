<template>
	<!-- 购彩订单详情列表 -->
	<view class="box">
		<u-navbar title="订单详情" bgColor="#3999FE" leftIconColor="#fff" :titleStyle="{color:'#fff'}" autoBack
			@rightClick="rightClick" :rightIcon="lotteryOrder.orderType=='1'?'more-dot-fill':''"
			style="margin-bottom: 70upx;"></u-navbar>
		<view class="info" style="background:#3999FE">
			<view style="display: flex;justify-content: space-around;align-items: center;padding-top: 84upx">
				<view>
					<p clsaa="data">{{lotteryOrder.winPrice|formatWinPrice}}</p>
					<p class="font">中奖金额</p>
				</view>
				<view>
					<p clsaa="data">{{lotteryOrder.state|formatState}}</p>
					<p class="font">方案状态</p>
				</view>
				<view>
					<p clsaa="data">{{lotteryOrder.price}}元</p>
					<p class="font" v-if="lotteryOrder.jointPurchaseFlag">合买发起金额</p>
					<p class="font" v-else>选号金额</p>
				</view>
			</view>
			<view style="padding-top: 20upx;">
				{{lotteryOrder.remark}}
			</view>
			<view style="padding-top: 44upx;padding-bottom:50upx"
				v-if="lotteryOrder.type=='0'||lotteryOrder.type=='1'||lotteryOrder.type=='2'||lotteryOrder.type=='9'||lotteryOrder.type=='14'||lotteryOrder.type=='15'">
				<u-button size="normal" shape="circle" customStyle="color:#000;width:92%;height:68upx" color="#fff">
					预测最高奖金：<span style="color:#00C3FF">￥{{lotteryOrder.forecast}}</span>
				</u-button>
			</view>
		</view>
		<view class="body">
			<uni-card is-shadow v-if="lotteryOrder.type=='3'||lotteryOrder.type=='10'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 999999;transform:rotate(10deg);">
					<image v-if="lotteryOrder.state=='4'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已兑奖.png"></image>
					<image v-if="lotteryOrder.state=='3'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已中奖.png"></image>
					<image v-if="lotteryOrder.state=='2'" style="width: 230px;height: 130px;"
						src="../../static/image/order/未中奖.png"></image>
				</view>
				<view>
					<span class="title">{{lotteryOrder.ballName}}</span>
					<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="lotteryOrder.notes+'注'" type="error"></u-tag>
						<u-tag :text="lotteryOrder.times +'倍'" style="margin-left: 10px;"></u-tag>
					</p>
				</view>
				<uni-table stripe emptyText="暂无更多数据">
					<!-- 表头行 -->
					<uni-tr>
						<uni-th :width="abo==undefined?'50':'20'" align="center">期号</uni-th>
						<uni-th :width="abo==undefined?'50':'100'" align="left">选号内容</uni-th>
						<uni-th :width="abo==undefined?'50':'30'" align="center">选号方式</uni-th>
						<!-- <uni-th :width="abo==undefined?'50':'30'" align="center" v-if="abo==undefined">赛果</uni-th> -->
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.recordList" :key="index">
						<uni-td :width="abo==undefined?'50':'30'" align="center">{{record.stageNumber}}</uni-td>
						<uni-td :width="abo==undefined?'50':'100'" align="left">
							<view class="selected_num" v-if="record.hundred!=null">
								<view>第一位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="a in record.hundred.toString().split(',')">
										<p>{{a}}</p>
									</div>
								</view>
							</view>

							<view class="selected_num" v-if="record.ten!=null">
								<view>{{record.mode==5?'选号':'第二位：'}}</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="b in record.ten.toString().split(',')">
										<p>{{b}}</p>
									</div>
								</view>
							</view>
							<view class="selected_num" v-if="record.individual!=null">
								<view>{{record.mode==0?'第三位：':'选号'}}</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-if="record.mode==1 || record.mode==2|| record.mode==6"
										v-for="c in record.individual">
										<p v-if="c.isGallbladder">
											{{'胆'+c.num}}
										</p>
										<p v-else>{{c.num}}</p>
									</div>
									<div style="background: ##F41515;"
										v-if="record.mode!=1 &&record.mode!=2&&record.mode!=6" class="content"
										v-for="c in record.individual.toString().split(',')">
										<p>{{c}}</p>
									</div>
								</view>
							</view>
						</uni-td>
						<uni-td :width="abo==undefined?'50':'30'" align="center">{{record.mode|formatMode}}</uni-td>
						<!-- <uni-td :width="abo==undefined?'50':'30'" align="center"
							v-if="abo==undefined">{{record.reward}}</uni-td> -->
					</uni-tr>
				</uni-table>

			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='4'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 999999;transform:rotate(10deg);">
					<image v-if="lotteryOrder.state=='4'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已兑奖.png"></image>
					<image v-if="lotteryOrder.state=='3'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已中奖.png"></image>
					<image v-if="lotteryOrder.state=='2'" style="width: 230px;height: 130px;"
						src="../../static/image/order/未中奖.png"></image>
				</view>
				<view>
					<span class="title">{{lotteryOrder.ballName}}</span>
					<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="lotteryOrder.notes+'注'" type="error"></u-tag>
						<u-tag :text="lotteryOrder.times +'倍'" style="margin-left: 10px;"></u-tag>
					</p>
				</view>
				<uni-table stripe emptyText="暂无更多数据">
					<!-- 表头行 -->
					<uni-tr>
						<uni-th width="15" align="center">期号</uni-th>
						<uni-th width="65" align="left">选号内容</uni-th>
						<!-- <uni-th width="65" align="center">赛果</uni-th> -->
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.recordList" :key="index">
						<uni-td align="center">{{record.stageNumber}}</uni-td>
						<uni-td align="left">
							<view class="selected_num">
								<view>第一位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="a in record.myriad.toString().split(',')">
										<p>{{a}}</p>
									</div>
								</view>
							</view>
							<view class="selected_num">
								<view>第二位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="a in record.kilo.toString().split(',')">
										<p>{{a}}</p>
									</div>
								</view>
							</view>
							<view class="selected_num">
								<view>第三位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="a in record.hundred.toString().split(',')">
										<p>{{a}}</p>
									</div>
								</view>
							</view>
							<view class="selected_num">
								<view>第四位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="b in record.ten.toString().split(',')">
										<p>{{b}}</p>
									</div>
								</view>
							</view>
							<view class="selected_num">
								<view>第五位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="c in record.individual.toString().split(',')">
										<p>{{c}}</p>
									</div>
								</view>
							</view>
						</uni-td>
						<!-- <uni-td align="center">{{record.reward}}</uni-td> -->
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='5'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 999999;transform:rotate(10deg);">
					<image v-if="lotteryOrder.state=='4'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已兑奖.png"></image>
					<image v-if="lotteryOrder.state=='3'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已中奖.png"></image>
					<image v-if="lotteryOrder.state=='2'" style="width: 230px;height: 130px;"
						src="../../static/image/order/未中奖.png"></image>
				</view>
				<view>
					<span class="title">{{lotteryOrder.ballName}}</span>
					<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="lotteryOrder.notes+'注'" type="error"></u-tag>
						<u-tag :text="lotteryOrder.times +'倍'" style="margin-left: 10px;"></u-tag>
					</p>
				</view>
				<uni-table stripe emptyText="暂无更多数据">
					<!-- 表头行 -->
					<uni-tr>
						<uni-th width="15" align="center">期号</uni-th>
						<uni-th width="65" align="left">选号内容</uni-th>
						<!-- <uni-th width="65" align="center">赛果</uni-th> -->
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.recordList" :key="index">
						<uni-td align="center">{{record.stageNumber}}</uni-td>
						<uni-td align="left">
							<view class="selected_num">
								<view>第一位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="a in record.hundredMyriad.toString().split(',')">
										<p>{{a}}</p>
									</div>
								</view>
							</view>

							<view class="selected_num">
								<view>第二位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="a in record.tenMyriad.toString().split(',')">
										<p>{{a}}</p>
									</div>
								</view>
							</view>

							<view class="selected_num">
								<view>第三位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="a in record.myriad.toString().split(',')">
										<p>{{a}}</p>
									</div>
								</view>
							</view>

							<view class="selected_num">
								<view>第四位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="a in record.kilo.toString().split(',')">
										<p>{{a}}</p>
									</div>
								</view>
							</view>

							<view class="selected_num">
								<view>第五位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="a in record.hundred.toString().split(',')">
										<p>{{a}}</p>
									</div>
								</view>
							</view>

							<view class="selected_num">
								<view>第六位：</view>
								<view>
									<div style="background: ##F41515;" class="content"
										v-for="b in record.ten.toString().split(',')">
										<p>{{b}}</p>
									</div>
								</view>
							</view>
							<view class="selected_num">
								<view>第七位：</view>
								<view>
									<div class="content" style="background-color: #007BED;"
										v-for="c in record.individual.toString().split(',')">
										<p>{{c}}</p>
									</div>
								</view>
							</view>
						</uni-td>
						<!-- <uni-td align="center">{{record.reward}}</uni-td> -->
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow
				v-if="lotteryOrder.type=='8'||lotteryOrder.type=='11'||lotteryOrder.type=='12'||lotteryOrder.type=='13'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 999999;transform:rotate(10deg);">
					<image v-if="lotteryOrder.state=='4'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已兑奖.png"></image>
					<image v-if="lotteryOrder.state=='3'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已中奖.png"></image>
					<image v-if="lotteryOrder.state=='2'" style="width: 230px;height: 130px;"
						src="../../static/image/order/未中奖.png"></image>
				</view>
				<view>
					<span class="title">{{lotteryOrder.ballName}}</span>
					<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="lotteryOrder.notes+'注'" type="error"></u-tag>
						<u-tag :text="lotteryOrder.times +'倍'" style="margin-left: 10px;"></u-tag>
						<u-tag v-if="lotteryOrder.append=='1'" :text="'追加'" type="success"
							style="margin-left: 10px;"></u-tag>
					</p>
				</view>
				<uni-table stripe emptyText="暂无更多数据">
					<!-- 表头行 -->
					<uni-tr>
						<uni-th width="15" align="center">期号</uni-th>
						<uni-th width="65" align="left">选号内容</uni-th>
						<uni-th width="55" v-if="lotteryOrder.type=='12'||lotteryOrder.type=='13'"
							align="center">选号方式</uni-th>
						<!-- <uni-th width="65" align="center">赛果</uni-th> -->
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.recordList" :key="index">
						<uni-td align="center">{{record.stageNumber}}</uni-td>
						<uni-td align="left">
							<view class="selected_num" v-if="record.ten!=null">
								<view v-if="lotteryOrder.type!='12'&&lotteryOrder.type!='13'">前区</view>
								<view>
									<div style="background: ##F41515;" class="content" v-for="b in record.ten">
										<p v-if="b.isGallbladder">
											{{'胆'+b.num}}
										</p>
										<p v-else>{{b.num}}</p>
									</div>
								</view>
							</view>

							<view class="selected_num" v-if="record.individual!=null">
								<view>后区</view>
								<view>
									<div style="background-color: #007BED;" class="content"
										v-for="c in record.individual">
										<p v-if="c.isGallbladder">
											{{'胆'+c.num}}
										</p>
										<p v-else>{{c.num}}</p>
									</div>
								</view>
							</view>
						</uni-td>
						<uni-th v-if="lotteryOrder.type=='12'">{{record.mode|formatQlcMode}}</uni-th>
						<uni-th v-if="lotteryOrder.type=='13'" align="center">{{record.mode|formatKl8Mode}}</uni-th>
						<!-- <uni-td align="center">{{record.reward}}</uni-td> -->
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='0'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 999999;transform:rotate(10deg);">
					<image v-if="lotteryOrder.state=='4'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已兑奖.png"></image>
					<image v-if="lotteryOrder.state=='3'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已中奖.png"></image>
					<image v-if="lotteryOrder.state=='2'" style="width: 230px;height: 130px;"
						src="../../static/image/order/未中奖.png"></image>
				</view>
				<view v-if="lotteryOrder.documentaryFlag&&!lotteryOrder.openFlag&&!lotteryOrder.isEnd"
					style="display: flex;flex-direction: column;justify-content: center;align-items: center;color: grey;font-size: 20px;">
					<u-icon name="lock" size="50" color="grey"></u-icon>
					<text style="margin-top: 10px;">开赛后可见</text>
					<text style="margin: 20px 0px;">截止时间 {{lotteryOrder.deadline|formatDate(that)}}</text>
				</view>
				<view v-else>
					<view>
						<span class="title">{{lotteryOrder.ballName}}</span>
						<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
							<u-tag :text="lotteryOrder.notes+'注'" type="error"></u-tag>
							<u-tag v-if="lotteryOrder.isOptimize=='0'" :text="lotteryOrder.times +'倍'"
								style="margin-left: 10px;"></u-tag>
							<view :key="index" v-for="(item,index) in lotteryOrder.pssTypeList">
								<u-tag v-if="item==1" :borderColor="index|mapTagColor" :bgColor="index|mapTagColor"
									text="单关" style="margin-left: 10px;">
								</u-tag>
								<u-tag v-else :borderColor="index|mapTagColor" :bgColor="index|mapTagColor"
									:text="item+'串一'" style="margin-left: 10px;">
								</u-tag>
							</view>
						</p>
					</view>
					<uni-table stripe emptyText="暂无更多数据">
						<!-- 表头行 -->
						<uni-tr>
							<uni-th width="10" align="center">场次</uni-th>
							<uni-th width="100" align="center">主队/客队</uni-th>
							<uni-th width="65" align="center">选号内容</uni-th>
							<uni-th width="50" align="center">赛果<br>(全/半)</uni-th>
						</uni-tr>
						<!-- 表格数据行 -->
						<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index">
							<uni-td align="center">{{record.number}}</uni-td>
							<uni-td align="center">{{record.homeTeam}}
								<span class="rangqiu"
									:class="{rangqiuBlue:record.letBall < 0}">({{record.letBall}})</span>
								<br> VS <br>{{record.visitingTeam}}
							</uni-td>
							<uni-td align="center">
								<!-- notLet.describe==record.award[0] 如果选择的和中奖的结果一样标记为红色 -->
								<span :style="notLet.describe==record.award[0]?'color:#FF3F43':''"
									v-for="(notLet,index) in record.content.notLetOddsList" v-if="notLet.active">
									{{notLet.describe}}({{notLet.odds}})<br>
								</span>

								<span :style="record.halfFullCourt!=undefined&&record.halfFullCourt!='比赛中断'&&lets.describe==(Number(record.halfFullCourt.split(',')[1].split(':')[0])+Number(record.letBall)>Number(record.halfFullCourt.split(',')[1].split(':')[1])?'胜'
								:Number(record.halfFullCourt.split(',')[1].split(':')[0])+Number(record.letBall)<Number(record.halfFullCourt.split(',')[1].split(':')[1])?'负'
								:'平')?'color:#FF3F43':''" v-for="(lets,index) in record.content.letOddsList" v-if="lets.active">
									让{{lets.describe}}({{lets.odds}})<br>
								</span>
								<span :style="goal.describe==record.award[2]?'color:#FF3F43':''"
									v-for="(goal,index) in record.content.goalOddsList" v-if="goal.active">
									{{goal.describe}}({{goal.odds}})<br>
								</span>
								<span :style="half.describe==record.award[3]?'color:#FF3F43':''"
									v-for="(half,index) in record.content.halfWholeOddsList" v-if="half.active">
									{{half.describe}}({{half.odds}})<br>
								</span>
								<span :style="score.describe==record.award[4]?'color:#FF3F43':''"
									v-for="(score,index) in record.content.scoreOddsList" v-if="score.active">
									{{score.describe}}({{score.odds}})<br>
								</span>
							</uni-td>
							<uni-td align="center">
								<span v-if="record.halfFullCourt!=undefined&&record.halfFullCourt!='比赛中断'">
									{{record.halfFullCourt.split(',')[1]}}<br>半{{record.halfFullCourt.split(',')[0]}}
								</span>
								<span v-else>
									{{record.halfFullCourt}}
								</span>
							</uni-td>
						</uni-tr>
					</uni-table>
				</view>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='1'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 999999;transform:rotate(10deg);">
					<image v-if="lotteryOrder.state=='4'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已兑奖.png"></image>
					<image v-if="lotteryOrder.state=='3'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已中奖.png"></image>
					<image v-if="lotteryOrder.state=='2'" style="width: 230px;height: 130px;"
						src="../../static/image/order/未中奖.png"></image>
				</view>
				<view v-if="lotteryOrder.documentaryFlag&&!lotteryOrder.openFlag&&!lotteryOrder.isEnd"
					style="display: flex;flex-direction: column;justify-content: center;align-items: center;color: grey;font-size: 20px;">
					<u-icon name="lock" size="50" color="grey"></u-icon>
					<text style="margin-top: 10px;">开赛后可见</text>
					<text style="margin: 20px 0px;">截止时间 {{lotteryOrder.deadline|formatDate(that)}}</text>
				</view>
				<view v-else>
					<view>
						<span class="title">{{lotteryOrder.ballName}}</span>
						<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
							<u-tag :text="lotteryOrder.notes+'注'" type="error"></u-tag>
							<u-tag v-if="lotteryOrder.isOptimize=='0'" :text="lotteryOrder.times +'倍'"
								style="margin-left: 10px;"></u-tag>
							<view :key="index" v-for="(item,index) in lotteryOrder.pssTypeList">
								<u-tag v-if="item==1" :borderColor="index|mapTagColor" :bgColor="index|mapTagColor"
									text="单关" style="margin-left: 10px;">
								</u-tag>
								<u-tag v-else :borderColor="index|mapTagColor" :bgColor="index|mapTagColor"
									:text="item+'串一'" style="margin-left: 10px;">
								</u-tag>
							</view>
						</p>
					</view>
					<uni-table stripe emptyText="暂无更多数据">
						<!-- 表头行 -->
						<uni-tr>
							<uni-th width="10" align="center">场次</uni-th>
							<uni-th width="100" align="center">客队/主队</uni-th>
							<uni-th width="75" align="center">选号内容</uni-th>
							<uni-th width="40" align="center">赛果</uni-th>
						</uni-tr>
						<!-- 表格数据行 -->
						<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index">
							<uni-td align="center">{{record.number}}</uni-td>
							<uni-td align="center">{{record.visitingTeam}}
								<br> VS <br>{{record.homeTeam}}
								<span class="rangqiu"
									:class="{rangqiuBlue:record.letBall < 0}">({{record.letBall}})</span>
							</uni-td>
							<uni-td align="center">
								<span :style="winNegative.describe==record.award[0]?'color:#FF3F43':''"
									v-for="(winNegative,index) in record.content.winNegativeOddsList"
									v-if="winNegative.active">
									{{winNegative.describe}}({{winNegative.odds}})<br>
								</span>
								<span :style="record.halfFullCourt!=undefined&&record.halfFullCourt!='比赛中断'&&cedePoints.describe==(Number(record.halfFullCourt.split(':')[1])+Number(record.letBall)>Number(record.halfFullCourt.split(':')[0])?'主胜'
								:'主负')?'color:#FF3F43':''" v-for="(cedePoints,index) in record.content.cedePointsOddsList"
									v-if="cedePoints.active">
									让{{cedePoints.describe}}({{cedePoints.odds}})<br>
								</span>
								<span :style="record.halfFullCourt!=undefined&&record.halfFullCourt!='比赛中断'&&size.describe==(Number(record.halfFullCourt.split(':')[1])+Number(record.halfFullCourt.split(':')[0])>Number(size.score)?'大'
								:'小')?'color:#FF3F43':''" v-for="(size,index) in record.content.sizeOddsList" v-if="size.active">
									{{size.describe}}<span
										style="color: #1afa29;">[{{size.score}}]</span>({{size.odds}})<br>
								</span>
								<span :style="difference.describe==record.award[2]?'color:#FF3F43':''"
									v-for="(difference,index) in record.content.differenceOddsList"
									v-if="difference.active">
									{{difference.describe}}({{difference.odds}})<br>
								</span>
							</uni-td>
							<uni-td align="center">
								<span v-if="record.halfFullCourt!=undefined">
									{{record.halfFullCourt}}
								</span>
							</uni-td>
						</uni-tr>
					</uni-table>
				</view>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='2'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 999999;transform:rotate(10deg);">
					<image v-if="lotteryOrder.state=='4'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已兑奖.png"></image>
					<image v-if="lotteryOrder.state=='3'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已中奖.png"></image>
					<image v-if="lotteryOrder.state=='2'" style="width: 230px;height: 130px;"
						src="../../static/image/order/未中奖.png"></image>
				</view>
				<view>
					<span class="title">{{lotteryOrder.ballName}}</span>
					<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="lotteryOrder.notes+'注'" type="error"></u-tag>
						<u-tag v-if="lotteryOrder.isOptimize=='0'" :text="lotteryOrder.times +'倍'"
							style="margin-left: 10px;"></u-tag>
						<view :key="index" v-for="(item,index) in lotteryOrder.pssTypeList">
							<u-tag v-if="item==1" :borderColor="index|mapTagColor" :bgColor="index|mapTagColor"
								text="单关" style="margin-left: 10px;">
							</u-tag>
							<u-tag v-else :borderColor="index|mapTagColor" :bgColor="index|mapTagColor"
								:text="item+'串一'" style="margin-left: 10px;">
							</u-tag>
						</view>
					</p>
				</view>
				<uni-table stripe emptyText="暂无更多数据">
					<!-- 表头行 -->
					<uni-tr>
						<uni-th width="10" align="center">场次</uni-th>
						<uni-th width="100" align="center">主队/客队</uni-th>
						<uni-th width="65" align="center">选号内容</uni-th>
						<uni-th width="50" align="center">赛果<br><span
								v-if="lotteryOrder.type=='2'">(全/半)</span></uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index">
						<uni-td align="center">{{record.number}}</uni-td>
						<uni-td align="center">{{record.homeTeam}}
							<span class="rangqiu" :class="{rangqiuBlue:record.letBall < 0}">({{record.letBall}})</span>
							<br> VS <br>{{record.visitingTeam}}
						</uni-td>
						<uni-td align="center">
							<span :style="record.halfFullCourt!=undefined&&record.halfFullCourt!='比赛中断'&&record.halfFullCourt.indexOf('-')==-1&&lets.describe==(Number(record.halfFullCourt.split(',')[1].split(':')[0])+Number(record.letBall)>Number(record.halfFullCourt.split(',')[1].split(':')[1])?'胜'
								:Number(record.halfFullCourt.split(',')[1].split(':')[0])+Number(record.letBall)<Number(record.halfFullCourt.split(',')[1].split(':')[1])?'负'
								:'平')?'color:#FF3F43':''" v-for="(lets,index) in record.content.letOddsList" v-if="lets.active">
								<!-- 判断如果中了就显示开奖后的赔率，没中的就显示开始下注的赔率 -->
								<span v-if="record.halfFullCourt!=undefined&&record.halfFullCourt!='比赛中断'&&record.halfFullCourt.indexOf('-')==-1&&lets.describe==(Number(record.halfFullCourt.split(',')[1].split(':')[0])+Number(record.letBall)>Number(record.halfFullCourt.split(',')[1].split(':')[1])?'胜'
								:Number(record.halfFullCourt.split(',')[1].split(':')[0])+Number(record.letBall)<Number(record.halfFullCourt.split(',')[1].split(':')[1])?'负'
								:'平')">让{{lets.describe}}({{record.bonusOdds[0]}})</span>
								<span v-else>让{{lets.describe}}({{lets.odds}})</span><br>
							</span>
							<span :style="goal.describe==record.award[1]?'color:#FF3F43':''"
								v-for="(goal,index) in record.content.goalOddsList" v-if="goal.active">
								<!-- 判断如果中了就显示开奖后的赔率，没中的就显示开始下注的赔率 -->
								<span v-if="goal.describe==record.award[1]">
									{{goal.describe}}({{record.bonusOdds[1]}})
								</span>
								<span v-else>
									{{goal.describe}}({{goal.odds}})
								</span>
								<br>
							</span>
							<span :style="score.describe==record.award[2]?'color:#FF3F43':''"
								v-for="(score,index) in record.content.scoreOddsList" v-if="score.active">
								<!-- 判断如果中了就显示开奖后的赔率，没中的就显示开始下注的赔率 -->
								<span v-if="score.describe==record.award[2]">
									{{score.describe}}({{record.bonusOdds[2]}})
								</span>
								<span v-else>
									{{score.describe}}({{score.odds}})
								</span>
								<br>
							</span>
							<span :style="oddEven.describe==record.award[3]?'color:#FF3F43':''"
								v-for="(oddEven,index) in record.content.oddEvenOdds" v-if="oddEven.active">
								<!-- 判断如果中了就显示开奖后的赔率，没中的就显示开始下注的赔率 -->
								<span v-if="oddEven.describe==record.award[3]">
									{{oddEven.describe}}({{record.bonusOdds[3]}})
								</span>
								<span v-else>
									{{oddEven.describe}}({{oddEven.odds}})
								</span>
								<br>
							</span>
							<span :style="half.describe==record.award[4]?'color:#FF3F43':''"
								v-for="(half,index) in record.content.halfWholeOddsList" v-if="half.active">
								<!-- 判断如果中了就显示开奖后的赔率，没中的就显示开始下注的赔率 -->
								<span v-if="half.describe==record.award[4]">
									{{half.describe}}({{record.bonusOdds[4]}})
								</span>
								<span v-else>
									{{half.describe}}({{half.odds}})
								</span>
								<br>
							</span>
						</uni-td>
						<uni-td align="center">
							<span v-if="record.halfFullCourt!=undefined&&record.halfFullCourt!='比赛中断'">
								{{record.halfFullCourt.split(',')[1]}}<br>半{{record.halfFullCourt.split(',')[0]}}
							</span>
							<span v-else>
								{{record.halfFullCourt}}
							</span>
						</uni-td>
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='9'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 999999;transform:rotate(10deg);">
					<image v-if="lotteryOrder.state=='4'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已兑奖.png"></image>
					<image v-if="lotteryOrder.state=='3'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已中奖.png"></image>
					<image v-if="lotteryOrder.state=='2'" style="width: 230px;height: 130px;"
						src="../../static/image/order/未中奖.png"></image>
				</view>
				<view>
					<span class="title">{{lotteryOrder.ballName}}</span>
					<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="lotteryOrder.notes+'注'" type="error"></u-tag>
						<u-tag v-if="lotteryOrder.isOptimize=='0'" :text="lotteryOrder.times +'倍'"
							style="margin-left: 10px;"></u-tag>
						<view :key="index" v-for="(item,index) in lotteryOrder.pssTypeList">
							<u-tag v-if="item==1" :borderColor="index|mapTagColor" :bgColor="index|mapTagColor"
								text="单关" style="margin-left: 10px;">
							</u-tag>
							<u-tag v-else :borderColor="index|mapTagColor" :bgColor="index|mapTagColor"
								:text="item+'串一'" style="margin-left: 10px;">
							</u-tag>
						</view>
					</p>
				</view>
				<uni-table stripe emptyText="暂无更多数据">
					<!-- 表头行 -->
					<uni-tr>
						<uni-th width="10" align="center">场次</uni-th>
						<uni-th width="100" align="center">主队/客队</uni-th>
						<uni-th width="65" align="center">选号内容</uni-th>
						<uni-th width="50" align="center">赛果<br></uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index">
						<uni-td align="center">{{record.number}}</uni-td>
						<uni-td align="center">{{record.homeTeam}}
							<span class="rangqiu" :class="{rangqiuBlue:record.letBall < 0}">({{record.letBall}})</span>
							<br> VS <br>{{record.visitingTeam}}
						</uni-td>
						<uni-td align="center">
							<span :style="record.halfFullCourt!=undefined&&record.halfFullCourt!='比赛中断'&&lets.describe==(Number(record.halfFullCourt.split(':')[0])+Number(record.letBall)>Number(record.halfFullCourt.split(':')[1])?'胜'
															:Number(record.halfFullCourt.split(':')[0])+Number(record.letBall)<Number(record.halfFullCourt.split(':')[1])?'负'
															:'平')?'color:#FF3F43':''" v-for="(lets,index) in record.content.letOddsList" v-if="lets.active">
								<!-- 判断如果中了就显示开奖后的赔率，没中的就显示开始下注的赔率 -->
								<span v-if="record.halfFullCourt!=undefined&&record.halfFullCourt!='比赛中断'&&lets.describe==(Number(record.halfFullCourt.split(':')[0])+Number(record.letBall)>Number(record.halfFullCourt.split(':')[1])?'胜'
															:Number(record.halfFullCourt.split(':')[0])+Number(record.letBall)<Number(record.halfFullCourt.split(':')[1])?'负'
															:'平')">让{{lets.describe}}({{record.bonusOdds[0]}})</span>
								<span v-else>让{{lets.describe}}({{lets.odds}})</span><br>
							</span>
						</uni-td>
						<uni-td align="center">
							<span v-if="record.halfFullCourt!=undefined">
								{{record.halfFullCourt}}
							</span>
						</uni-td>
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='6'||lotteryOrder.type=='7'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 999999;transform:rotate(10deg);">
					<image v-if="lotteryOrder.state=='4'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已兑奖.png"></image>
					<image v-if="lotteryOrder.state=='3'" style="width: 230px;height: 130px;"
						src="../../static/image/order/已中奖.png"></image>
					<image v-if="lotteryOrder.state=='2'" style="width: 230px;height: 130px;"
						src="../../static/image/order/未中奖.png"></image>
				</view>
				<view>
					<span class="title">{{lotteryOrder.ballName}}</span>
					<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="lotteryOrder.notes+'注'" type="error"></u-tag>
						<u-tag :text="lotteryOrder.times +'倍'" style="margin-left: 10px;"></u-tag>
						<view :key="index" v-for="(item,index) in lotteryOrder.pssTypeList">
							<u-tag v-if="item==1" :borderColor="index|mapTagColor" :bgColor="index|mapTagColor"
								text="单关" style="margin-left: 10px;">
							</u-tag>
						</view>
					</p>
				</view>
				<uni-table stripe emptyText="暂无更多数据">
					<!-- 表头行 -->
					<uni-tr>
						<uni-th width="10" align="center">场次</uni-th>
						<uni-th width="100" align="center">主队/客队</uni-th>
						<uni-th width="65" align="center">选号内容</uni-th>
						<uni-th width="50" align="center">赛果</uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index">
						<uni-td align="center">
							<div class="paiban">
								<div>
									<view class="demo-layout bg-purple">{{record.number}}</view>
								</div>
								<div style="background:#00C3FF" class="content" v-if="record.content.isGallbladder">
									<p>胆</p>
								</div>
							</div>
						</uni-td>
						<uni-td align="center">{{record.homeTeam}}
							<br> VS <br>{{record.visitingTeam}}
						</uni-td>
						<uni-td align="center">
							<!-- notLet.describe==record.award[0] 如果选择的和中奖的结果一样标记为红色 -->
							<span :style="notLet.describe==record.award[0]?'color:#FF3F43':''"
								v-for="(notLet,index) in record.content.notLetOddsList" v-if="notLet.active">
								{{notLet.describe}}({{notLet.odds}})<br>
							</span>
						</uni-td>
						<uni-td align="center">
							<span v-if="record.award!=undefined">
								{{record.award[0]}}
							</span>
						</uni-td>
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='14'">
				<view>
					<span class="title">{{lotteryOrder.ballName}}</span>
					<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="lotteryOrder.notes+'项'" type="error"></u-tag>
						<u-tag :text="lotteryOrder.times +'倍'" style="margin-left: 10px;"></u-tag>
					</p>
				</view>
				<uni-table stripe emptyText="暂无更多数据">
					<!-- 表头行 -->
					<uni-tr>
						<uni-th align="center">编号</uni-th>
						<uni-th align="center">队伍</uni-th>
						<uni-th align="center">赛果</uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index">
						<uni-td align="center">
							{{record.content.number}}
						</uni-td>
						<uni-td align="center">
							{{record.content.ranks}}({{record.content.odds}})
						</uni-td>
						<uni-td align="center">
							<span v-if="record.award[0]=='0'||record.award[0]=='2'">
								等待开奖
							</span>
							<span v-if="record.award[0]=='1'" :style="record.award[0]=='1'?'color:#FF3F43':''">
								已胜出
							</span>
							<span v-if="record.award[0]=='3'">
								已出局
							</span>
						</uni-td>
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='15'">
				<view>
					<span class="title">{{lotteryOrder.ballName}}</span>
					<p style="display: flex;justify-content: flex-end;align-items: center;flex-wrap: wrap;">
						<u-tag :text="lotteryOrder.notes+'项'" type="error"></u-tag>
						<u-tag :text="lotteryOrder.times +'倍'" style="margin-left: 10px;"></u-tag>
					</p>
				</view>
				<uni-table stripe emptyText="暂无更多数据">
					<!-- 表头行 -->
					<uni-tr>
						<uni-th align="center">编号</uni-th>
						<uni-th align="center">队伍</uni-th>
						<uni-th align="center">赛果</uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index">
						<uni-td align="center">
							{{record.content.number}}
						</uni-td>
						<uni-td align="center">
							<div style="display: flex;justify-content: center;align-items: center;">
								<span>
									{{record.content.homeTeam}}
									<br> VS <br>
									{{record.content.visitingTeam}}
								</span>
								<span>({{record.content.odds}})</span>
							</div>
						</uni-td>
						<uni-td align="center">
							<span v-if="record.award[0]=='0'||record.award[0]=='2'">
								等待开奖
							</span>
							<span v-if="record.award[0]=='1'" :style="record.award[0]=='1'?'color:#FF3F43':''">
								已胜出
							</span>
							<span v-if="record.award[0]=='3'">
								已出局
							</span>
						</uni-td>
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card class="phone"
				v-if="lotteryOrder.schemeDetails!=null&&lotteryOrder.schemeDetails!=undefined&&lotteryOrder.schemeDetails!=''&&lotteryOrder.type!=3&&lotteryOrder.type!=4&&lotteryOrder.type!=5&&lotteryOrder.type!=8&&lotteryOrder.type!=10&&lotteryOrder.type!=11&&lotteryOrder.type!=12&&lotteryOrder.type!=13&&lotteryOrder.type!=14&&lotteryOrder.type!=15||lotteryOrder.schemeDetails=='none'&&lotteryOrder.type!=3&&lotteryOrder.type!=4&&lotteryOrder.type!=5&&lotteryOrder.type!=8&&lotteryOrder.type!=10&&lotteryOrder.type!=11&&lotteryOrder.type!=12&&lotteryOrder.type!=13&&lotteryOrder.type!=14&&lotteryOrder.type!=15">
				<p>
					<view class="title" v-if="lotteryOrder.isOptimize=='1'">优化后方案详情</view>
					<view class="title" v-else>中奖方案详情</view>
					<!-- 展开/收起按钮 -->
					<view class="btn_expanded" @click="toggleListRate">
						<view class="toggle-button">
							{{ !isExpandedRate ? '收起' : '展开方案' }}
						</view>
						<view class="expanded">
							<img :src="!isExpandedRate ? expandedImg : expandedCutImg" alt="" class="expandedImg">
						</view>
					</view>
					<uni-table border stripe emptyText="暂无更多数据" class="make" v-if="!isExpandedRate">
						<!-- 表头行 -->
						<uni-tr>
							<uni-th width="40px" align="center">过关</uni-th>
							<uni-th align="center" width="100px">单注组合</uni-th>
							<uni-th align="center" width="30px">倍数</uni-th>
							<uni-th align="center" width="80px">预测奖金</uni-th>
						</uni-tr>
						<!-- 表格数据行 -->
						<tbody v-for="(item,index) in lotteryOrder.schemeDetails" :key="index">
							<uni-tr>
								<uni-td align="center">{{item.type}}</uni-td>
								<uni-td align="center">
									<view @click="open(index,item)"
										style="display: flex;justify-content: center;align-items: center;flex-direction: column;font-size: 12px;">
										<span v-for="(ball,idx) in item.ballCombinationList" :key="idx">
											{{ball.homeTeam}}|{{ball.content}}<br>
										</span>
										<u-icon name="arrow-down"></u-icon>
									</view>
								</uni-td>
								<uni-td align="center">
									{{item.notes}}
								</uni-td>
								<uni-td align="center"
									v-if="lotteryOrder.type!=6 && lotteryOrder.type!=7">{{item.forecastBonus}}</uni-td>
								<uni-td align="center" v-else>
									浮动奖金<br>
									以官网<br>
									出奖为准
								</uni-td>
							</uni-tr>

							<uni-tr v-if="item.isShow" style="background: #FAF9DE">
								<uni-th width="10px" align="center">场次</uni-th>
								<uni-th align="center" width="85px">主队</uni-th>
								<uni-th align="center" width="85px">客队</uni-th>
								<uni-th align="center" width="80px">选号内容</uni-th>
							</uni-tr>
							<uni-tr v-for="(data,idx) in item.ballCombinationList" :key="idx" v-if="item.isShow"
								style="background: #FAF9DE">
								<uni-td align="center">{{data.number}}</uni-td>
								<uni-td align="center">{{data.homeTeam}}</uni-td>
								<uni-td align="center">{{data.visitingTeam}}</uni-td>
								<uni-td align="center">{{data.content}}</uni-td>
							</uni-tr>
						</tbody>
					</uni-table>
				</p>
			</uni-card>
			
			<uni-card class="phone" v-if="(lotteryOrder.type=='3'
				||lotteryOrder.type=='4'
				||lotteryOrder.type=='5'
				||lotteryOrder.type=='8'
				||lotteryOrder.type=='10'
				||lotteryOrder.type=='11'
				||lotteryOrder.type=='12'
				||lotteryOrder.type=='13')
				&&
				lotteryOrder.recordList[0].reward!=null
				&&
				lotteryOrder.recordList[0].reward!='' 
				">
				<view class="title" style="display: flex;justify-content: space-around;align-items: center;">
					<view>
						开奖号码：
					</view>
					<view class="selected_num">
						<view>
							<div style="background: #F41515;" class="content"
								v-for="c in lotteryOrder.recordList[0].reward.split(',')">
								<p>{{c}}</p>
							</div>
						</view>
					</view>
				</view>
			</uni-card>

			<uni-card class="phone"
				v-if="lotteryOrder.schemeDetails!=null&&lotteryOrder.schemeDetails!=undefined&&lotteryOrder.schemeDetails!=''&&lotteryOrder.type==14||lotteryOrder.schemeDetails!=null&&lotteryOrder.schemeDetails!=undefined&&lotteryOrder.schemeDetails!=''&&lotteryOrder.type==15">
				<p>
					<view class="title" v-if="lotteryOrder.isOptimize=='1'">优化后方案详情</view>
					<view class="title" v-else>中奖方案详情</view>
					<!-- 展开/收起按钮 -->
					<view class="btn_expanded" @click="toggleListRate">
						<view class="toggle-button">
							{{ !isExpandedRate ? '收起' : '展开方案' }}
						</view>
						<view class="expanded">
							<img :src="!isExpandedRate ? expandedImg : expandedCutImg" alt="" class="expandedImg">
						</view>
					</view>
					<uni-table border stripe emptyText="暂无更多数据" class="make" v-if="!isExpandedRate">
						<!-- 表头行 -->
						<uni-tr>
							<uni-th width="10px" align="center">编号</uni-th>
							<uni-th width="40px" align="center">队伍</uni-th>
							<uni-th align="center" width="40px">倍数</uni-th>
							<uni-th align="center" width="40px">预测奖金</uni-th>
						</uni-tr>
						<!-- 表格数据行 -->
						<tbody v-for="(item,index) in lotteryOrder.schemeDetails" :key="index">
							<uni-tr>
								<uni-td align="center">{{item.number}}</uni-td>
								<uni-td v-if="lotteryOrder.type==14" align="center">{{item.ranks}}</uni-td>
								<uni-td v-if="lotteryOrder.type==15" align="center">{{item.homeTeam}} <br> VS<br>
									{{item.visitingTeam}}</uni-td>
								<uni-td align="center">
									{{item.notes}}
								</uni-td>
								<uni-td align="center">{{item.total}}</uni-td>
							</uni-tr>
						</tbody>
					</uni-table>
				</p>
			</uni-card>


			<uni-card class="phone"
				v-if="lotteryOrder.schemeDetails!=null&&lotteryOrder.schemeDetails!=undefined&&lotteryOrder.schemeDetails!=''&&lotteryOrder.type==3||lotteryOrder.type==4||lotteryOrder.type==5||lotteryOrder.type==8||lotteryOrder.type==10||lotteryOrder.type==11||lotteryOrder.type==12||lotteryOrder.type==13||lotteryOrder.schemeDetails=='none'&&lotteryOrder.type!=0&&lotteryOrder.type!=1&&lotteryOrder.type!=2&&lotteryOrder.type!=6&&lotteryOrder.type!=7&&lotteryOrder.type!=9&&lotteryOrder.type!=14&&lotteryOrder.type!=15">
				<p>
					<view class="title">中奖方案详情</view>
					<!-- 展开/收起按钮 -->
					<view class="btn_expanded" @click="toggleListRate">
						<view class="toggle-button">
							{{ !isExpandedRate ? '收起' : '展开方案' }}
						</view>
						<view class="expanded">
							<img :src="!isExpandedRate ? expandedImg : expandedCutImg" alt="" class="expandedImg">
						</view>
					</view>
					<uni-table border stripe emptyText="暂无更多数据" class="make" v-if="!isExpandedRate">
						<!-- 表头行 -->
						<uni-tr>
							<uni-th width="20px" align="center">期号</uni-th>
							<uni-th align="center" width="30px"
								v-if="lotteryOrder.type==3||lotteryOrder.type==13">过关</uni-th>
							<uni-th align="center" width="80px">中奖组合</uni-th>
							<uni-th align="center" width="30px">倍数</uni-th>
							<uni-th align="center" width="120px">预测奖金</uni-th>
						</uni-tr>
						<!-- 表格数据行 -->
						<tbody v-for="(item,index) in lotteryOrder.schemeDetails" :key="index">
							<uni-tr>
								<uni-td align="center">{{item.stageNumber}}</uni-td>
								<uni-td align="center" v-if="lotteryOrder.type==3">
									{{item.mode|formatMode}}
								</uni-td>
								<uni-td align="center" v-if="lotteryOrder.type==13">
									{{item.mode|formatKl8Mode}}
								</uni-td>
								<uni-td align="center">
									{{item.content}}
								</uni-td>
								<uni-td align="center">
									{{lotteryOrder.times}}
								</uni-td>
								<uni-td align="center"
									v-if="lotteryOrder.type==3||lotteryOrder.type==4">{{item.forecastBonus}}</uni-td>
								<uni-td align="center" v-else>
									浮动奖金以官网出奖为准
								</uni-td>
							</uni-tr>
						</tbody>
					</uni-table>
				</p>
			</uni-card>

			<uni-card is-shadow class="tip phone">
				<view class="uni-body" @click="copy(lotteryOrder.remarkName)">用户名称：{{lotteryOrder.remarkName}}
					<image src="../../static/image/personal/复制.png"
						style="width: 32upx;height: 32upx;padding-left: 20upx;margin-bottom: -8upx;"></image>
				</view>
				<view class="uni-body" @click="copy(lotteryOrder.ticketOwner)">出票店主：{{lotteryOrder.ticketOwner}}
					<image src="../../static/image/personal/复制.png"
						style="width: 32upx;height: 32upx;padding-left: 20upx;margin-bottom: -8upx;"></image>
				</view>
				<view class="uni-body" @click="copy(lotteryOrder.orderId)">方案编号：{{lotteryOrder.orderId}}
					<image src="../../static/image/personal/复制.png"
						style="width: 32upx;height: 32upx;padding-left: 20upx;margin-bottom: -8upx;"></image>
				</view>
				<view class="uni-body">创建时间：{{lotteryOrder.createTime|formatDate(that)}}</view>
				<view class="uni-body">出票时间：{{lotteryOrder.ticketingTime|formatDate(that)}}</view>
				<view class="uni-body" v-if="lotteryOrder.type=='0'||lotteryOrder.type=='1'">提示：赔率以实际出票的赔率为准</view>
				<view class="uni-body" v-else>提示：赔率以最后出奖的赔率为准</view>
				<!-- <view class="uni-body">提示：中奖金额以实际出奖金额的为准</view> -->
			</uni-card>

			<uni-card class="phone" v-if="lotteryOrder.bill!=undefined">
				<p>
					<view class="title">彩票照片</view>
					<u-image v-if="lotteryOrder.bill!=undefined" @click="imgListPreview(lotteryOrder.bill)"
						:src="lotteryOrder.bill"
						style="display: flex;justify-content: center;align-items: center;margin-top: 20px;"></u-image>
				</p>
			</uni-card>
		</view>
		<view style="position: fixed;bottom: 10upx;left: 50%;width: 95%;  transform: translateX(-50%);"
			v-if="lotteryOrder.state==0||lotteryOrder.state==1">
			<view style="display: flex;justify-content: center;align-items: center;width: 100%;margin: 0 auto;">
				<u-button
					v-if="lotteryOrder.state=='0'&&lotteryOrder.meetShopId==null||lotteryOrder.state=='0'&&lotteryOrder.meetShopId==shop.id"
					color="#3999FE" text="出票" @click="handleTicketing(lotteryOrder,'1')"></u-button>
				<u-button
					v-if="lotteryOrder.meetShopId==null&&lotteryOrder.state!='6'&&lotteryOrder.state!='8'&&lotteryOrder.state!='7'||lotteryOrder.meetShopId==shop.id&&lotteryOrder.state!='6'&&lotteryOrder.state!='8'&&lotteryOrder.state!='7'"
					color="#3999FE" text="退票" customStyle="margin-left: 10px"
					@click="handleRetreat(lotteryOrder)"></u-button>
				<u-button
					v-if="lotteryOrder.meetShopId==null&&lotteryOrder.state!='6'&&lotteryOrder.state!='8'&&lotteryOrder.state!='7'||lotteryOrder.meetShopId==shop.id&&lotteryOrder.state!='6'&&lotteryOrder.state!='8'&&lotteryOrder.state!='7'"
					color="#3999FE" text="实票上传" customStyle="margin-left: 10px"
					@click="handleActualVote(lotteryOrder)"></u-button>
			</view>
		</view>
		<u-popup :show="actualVoteDialogVisible" @close="actualVoteDialogVisible=false" @open="actualOpen">
			<view>
				<uni-notice-bar show-icon text="为避免店铺被恶意投诉,拍票时请务必遮挡票面密码,站点号,地址,二维码等信息" background-color="#fff"
					color='rgb(255, 0, 0)' />
			</view>
			<view style="display: flex;justify-content: center;align-items: center;margin-top: 30px;margin-left: 10px;">
				<text>票据：</text>
				<u-upload :previewFullImage="true" @delete="deletePic" maxCount="1" width="200" height="200"
					:fileList="fileList" @afterRead="afterRead"></u-upload>
			</view>
			<view style="display: flex;justify-content: center;align-items: center;margin-bottom: 10px;">
				<u-button text="取消" customStyle="margin: 10px" @click="actualVoteDialogVisible = false"></u-button>
				<u-button color="#3999FE" text="出票" customStyle="margin: 10px" @click="operationActualVote"></u-button>
			</view>
		</u-popup>
		<u-modal :showCancelButton="true" @cancel="retreatShow=false" @close="retreatShow=false" :show="retreatShow"
			:zoom="false" :closeOnClickOverlay="true" @confirm="confirm">
			<view style="display: flex;justify-content:space-between;align-items: center;flex-wrap: wrap;gap: 10upx;">
				<u-button :type="retreatParam.remark=='订单逾期'?'primary':'info'" text="订单逾期"
					@click="remarkBtn('订单逾期')"></u-button>
				<u-button :type="retreatParam.remark=='票数太多'?'primary':'info'" text="票数太多"
					@click="remarkBtn('票数太多')"></u-button>
				<u-button :type="retreatParam.remark=='订单较多,忙不过来'?'primary':'info'" text="订单较多,忙不过来"
					@click="remarkBtn('订单较多,忙不过来')"></u-button>
				<u-button :type="retreatParam.remark=='联系不到客户'?'primary':'info'" text="联系不到客户"
					@click="remarkBtn('联系不到客户')"></u-button>
				<u-button :type="retreatParam.remark=='中心限制订单,无法出票'?'primary':'info'" text="中心限制订单,无法出票"
					@click="remarkBtn('中心限制订单,无法出票')"></u-button>
				<u-button :type="retreatParam.remark=='来不及出票,赔率变化'?'primary':'info'" text="来不及出票,赔率变化"
					@click="remarkBtn('来不及出票,赔率变化')"></u-button>
				<u-input border="bottom" v-model="retreatParam.remark" clearable placeholder="自定义"
					inputAlign="center" />
			</view>
		</u-modal>
	</view>
</template>

<script>
	import cfg from '@/util/environment.js';
	import {
		tick,
		retreat,
		getLotteryOrderById,
		updateOrderActualVote
	} from '@/api/lotteryOrder.js'
	import {
		delFile
	} from '@/api/upload.js'
	export default {
		data() {
			return {
				isExpandedRate: true, // 默认情况下不展开
				expandedImg: require('../../static/image/home/收起.png'),
				expandedCutImg: require('../../static/image/home/展开.png'),
				lotteryOrder: {},
				that: this,
				type: "1",
				id: "",
				individualLeng: 0,
				tenLeng: 0,
				ticketing: {
					state: "",
					id: "",
				},
				retreatShow: false,
				disable: true,
				fileList: [],
				actualVote: {
					id: "",
					bill: ""
				},
				delFileParam: {
					key: "",
					minioKey: ""
				},
				retreatParam: {
					id: null,
					remark: null
				},
				actualVoteDialogVisible: false,
			}
		},
		onLoad(option) {
			this.init(option.id);
			this.id = option.id
			//标识是入口进来的
			this.abo = option.abo
			this.shop = uni.getStorageSync("shop");
		},
		filters: {
			mapTagColor(index) {
				switch (index % 20) {
					case 0:
						return '#D52BB3'
					case 1:
						return '#3CC48D'
					case 2:
						return '#d81e06'
					case 3:
						return '#1296db'
					case 4:
						return '#13227a'
					case 5:
						return '#3cc2a8'
					case 6:
						return '#1cedf2'
					case 7:
						return '#6b1cf2'
					case 8:
						return '#e71cf2'
					case 9:
						return '#f28a1c'
					case 10:
						return '#f4ea2a'
					default:
						return '#19be6b'
				}
			},
			formatKl8Mode(mode) {
				if (mode == "1") {
					return "选一";
				} else if (mode == "2") {
					return "选二";
				} else if (mode == "3") {
					return "选三";
				} else if (mode == "4") {
					return "选四";
				} else if (mode == "5") {
					return "选五";
				} else if (mode == "6") {
					return "选六";
				} else if (mode == "7") {
					return "选七";
				} else if (mode == "8") {
					return "选八";
				} else if (mode == "9") {
					return "选九";
				} else if (mode == "10") {
					return "选十";
				}
			},
			formatMode(mode) {
				if (mode == "0") {
					return "直选";
				} else if (mode == "1") {
					return "组三";
				} else if (mode == "2") {
					return "组六";
				} else if (mode == "3") {
					return "直选和值";
				} else if (mode == "4") {
					return "组选和值";
				} else if (mode == "5") {
					return "组三复式";
				} else if (mode == "6") {
					return "直选复式";
				}
			},
			formatQlcMode(mode) {
				if (mode == "7") {
					return "选7";
				} else if (mode == "8") {
					return "选8";
				} else if (mode == "9") {
					return "选9";
				} else if (mode == "10") {
					return "选10";
				} else if (mode == "11") {
					return "选11";
				} else if (mode == "12") {
					return "选12";
				} else if (mode == "13") {
					return "选13";
				} else if (mode == "14") {
					return "选14";
				} else if (mode == "15") {
					return "选15";
				}
			},
			//格式化日期
			formatDate(data, that) {
				if (data != null && data != undefined) {
					return that.globalUtil.dateTimeFormat(data)
				}
			},
			formatWinPrice(data) {
				if (data == null || data == undefined || data == 0) {
					return "暂无";
				}
				return data;
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
					return "拒绝";
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
			confirm() {
				if (this.disable) {
					this.disable = false;
					retreat(this.retreatParam).then(res => {
						if (res.success) {
							uni.showToast({
								title: "操作成功",
								icon: "success"
							})
							uni.navigateBack()
							this.init(this.id)
							this.disable = true;
						}
					}).catch(e => {
						this.disable = true;
					})
				}
			},
			remarkBtn(text) {
				this.retreatParam.remark = text
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
			rightClick() {
				uni.navigateTo({
					url: "/pages/purchase/info?id=" + this.lotteryOrder.jointPurchaseId
				})
			},
			//实体票图片入库
			operationActualVote() {
				if (this.fileList.length <= 0) {
					uni.showToast({
						title: '实体票图片不能为空',
						icon: 'none'
					});
					return;
				}
				this.lotteryOrder.bill = this.actualVote.bill;
				updateOrderActualVote(this.actualVote).then(res => {
					if (res.success) {
						this.handleTicketing(this.lotteryOrder, '1')
						this.actualVoteDialogVisible = false;
					}
					s
				})
			},
			async afterRead(event) {
				let lists = [].concat(event.file)
				let fileListLen = this[`fileList`].length
				lists.map((item) => {
					this[`fileList`].push({
						...item,
						status: 'uploading',
						message: '上传中'
					})
				})
				for (let i = 0; i < lists.length; i++) {
					// 将blob转为file对象的方法
					function blobToFile(blob, fileName) {
						return new File([blob], fileName, {
							type: 'image/png'
						})
					}
					// 获取blob对象
					fetch(lists[i].url)
						.then(response => response.blob())
						.then(async blob => {
							// 将blob转换为file
							let fileN = blobToFile(blob, lists[i].name)
							const result = await this.uploadFilePromise(fileN)
							let item = this[`fileList`][fileListLen]
							this[`fileList`].splice(fileListLen, 1, Object.assign(item, {
								status: 'success',
								message: '',
								url: result
							}))
							fileListLen++
						}).then(() => {
							this.actualVote.bill = this.fileList[0].url;
						})

				}
			},
			//文件上传
			uploadFilePromise(url) {
				console.log(url)
				return new Promise((resolve, reject) => {
					let a = uni.uploadFile({
						url: cfg.BASE_API + "/file/sys/upload",
						file: url,
						name: 'file',
						header: {
							'x-access-token': uni.getStorageSync("vue_authtoken"),
							"x-sys": "1",
							'x-user': uni.getStorageSync("x-user"),
						},
						success: (res) => {
							let resData = JSON.parse(res.data);
							if (res.statusCode == 200 && resData.success) {
								setTimeout(() => {
									resolve(resData.url)
								}, 1000)
							} else {
								uni.$u.toast(resData.errorMsg)
								resolve("")
							}
						}
					});
				})
			},
			// 删除图片
			deletePic(event) {
				uni.showModal({
					title: '提示',
					confirmColor: "#3999FE",
					content: '是否确定要删除该图片?',
					success: res => {
						if (res.confirm) {
							this[`fileList`].splice(event.index, 1)
							this.delFileParam.key = event.file.name.substring(0, event.file.name.indexOf("."));
							this.delFileParam.minioKey = event.file.name;
							//将数据进行清理
							delFile(this.delFileParam);
						}
					}
				});
			},
			actualOpen() {
				this.ticketing.state = "";
			},
			handleActualVote(row) {
				this.fileList = [];
				this.actualVote.id = row.id;
				this.actualVote.bill = row.bill
				this.actualVoteDialogVisible = true;
				//如果有票据了就需要展示出来
				if (this.actualVote.bill != null && this.actualVote.bill != "") {
					this.fileList.push({
						url: row.bill,
						name: row.bill.substring(row.bill.lastIndexOf("/") + 1)
					})
				}
			},
			handleRetreat(item) {
				this.retreatParam.id = item.id
				this.retreatShow = true;
			},
			handleTicketing(item, state) {
				if (item.isActualTicket == "1" && item.bill == null) {
					uni.showModal({
						title: '提示',
						confirmColor: "#3999FE",
						confirmText: "去上传实体票据",
						// cancelText: "忽略票据直接出票",
						cancelText: "取消",
						content: '客户要求店主上传实体票据?',
						success: res => {
							if (res.confirm) {
								this.actualVote.id = item.id;
								this.actualVote.bill = item.bill
								this.actualVoteDialogVisible = true;
								return;
							} else if (res.cancel) {
								// this.ticketing.id = item.id;
								// this.ticketing.state = state;
								// tick(this.ticketing).then(res => {
								// 	if (res.success) {
								// uni.showToast({
								// 	title:"操作成功",
								// 	icon:"success"
								// })
								// 		this.init()
								// 	}
								// })
							}
						}
					});
				} else {
					if (this.disable) {
						this.disable = false;
						this.ticketing.id = item.id;
						this.ticketing.state = state;
						tick(this.ticketing).then(res => {
							if (res.success) {
								uni.showToast({
									title: "操作成功",
									icon: "success"
								})
								uni.navigateBack()
								this.disable = true;
								this.init(this.id)
							}
						}).catch(err => {
							this.disable = true;
						})
					}
				}
			},
			toggleListRate() {
				this.isExpandedRate = !this.isExpandedRate; // 切换展开/收起状态
				if (this.isExpandedRate) {
					this.type = "1"
				} else {
					this.type = "0"
				}
				this.init(this.id)
			},
			open(index, item) {
				this.$set(this.lotteryOrder.schemeDetails[index], "isShow", !item.isShow)
			},
			imgListPreview(url) {
				var urlList = []
				urlList.push(url) //push中的参数为 :src="item.img_url" 中的图片地址
				uni.previewImage({
					indicator: "number",
					loop: true,
					urls: urlList
				})
			},
			//初始化事件
			init(id) {
				getLotteryOrderById(id, this.type).then(res => {
					this.lotteryOrder = res;
					if (res.schemeDetails != null && res.schemeDetails != undefined && res.schemeDetails != "" &&
						res.schemeDetails != "none") {
						this.lotteryOrder.schemeDetails = JSON.parse(this.lotteryOrder.schemeDetails)
						//过滤掉数字彩
						if (this.lotteryOrder.type != 3 && this.lotteryOrder.type != 4 && this.lotteryOrder.type !=
							5 && this.lotteryOrder.type != 8 &&
							this.lotteryOrder.type != 10 &&
							this.lotteryOrder.type != 11 &&
							this.lotteryOrder.type != 12 &&
							this.lotteryOrder.type != 13) {
							this.lotteryOrder.schemeDetails.map((item, index) => {
								if (this.lotteryOrder.isOptimize == "0") {
									item.forecastBonus = parseFloat((item.forecastBonus / item
											.notes) *
										this
										.lotteryOrder.times).toFixed(2)
									item.notes = this.lotteryOrder.times
								}
								/* let type = item.type.substring(0, item.type.indexOf("串"))
								let v = item.forecastBonus / item.notes;
								if (type == 1) {
									if (v > 100000) {
										if (this.lotteryOrder.isOptimize == "0") {
											item.forecastBonus = parseFloat(100000 * this.lotteryOrder
												.times).toFixed(2)
											item.notes = this.lotteryOrder.times
										} else {
											item.forecastBonus = parseFloat(100000 * item.notes).toFixed(2)
										}
									} else {
										if (this.lotteryOrder.isOptimize == "0") {
											item.forecastBonus = parseFloat((item.forecastBonus / item
													.notes) *
												this
												.lotteryOrder.times).toFixed(2)
											item.notes = this.lotteryOrder.times
										}
									}
								} else if (type == 2 || type == 3) {
									if (v > 200000) {
										if (this.lotteryOrder.isOptimize == "0") {
											item.forecastBonus = parseFloat(200000 * this.lotteryOrder
												.times).toFixed(2)
											item.notes = this.lotteryOrder.times
										} else {
											item.forecastBonus = parseFloat(200000 * item.notes).toFixed(2)
										}
									} else {
										if (this.lotteryOrder.isOptimize == "0") {
											item.forecastBonus = parseFloat((item.forecastBonus / item
													.notes) *
												this
												.lotteryOrder.times).toFixed(2)
											item.notes = this.lotteryOrder.times
										}
									}
								} else if (type == 4 || type == 5) {
									if (v > 500000) {
										if (this.lotteryOrder.isOptimize == "0") {
											item.forecastBonus = parseFloat(500000 * this.lotteryOrder
												.times).toFixed(2)
											item.notes = this.lotteryOrder.times
										} else {
											item.forecastBonus = parseFloat(500000 * item.notes).toFixed(2)
										}
									} else {
										if (this.lotteryOrder.isOptimize == "0") {
											item.forecastBonus = parseFloat((item.forecastBonus / item
													.notes) *
												this
												.lotteryOrder.times).toFixed(2)
											item.notes = this.lotteryOrder.times
										}
									}
								} else if (type >= 6) {
									if (v > 1000000) {
										if (this.lotteryOrder.isOptimize == "0") {
											item.forecastBonus = parseFloat(1000000 * this.lotteryOrder
												.times).toFixed(2)
											item.notes = this.lotteryOrder.times
										} else {
											item.forecastBonus = parseFloat(1000000 * item.notes).toFixed(
												2)
										}
									} else {
										if (this.lotteryOrder.isOptimize == "0") {
											item.forecastBonus = parseFloat((item.forecastBonus / item
													.notes) *
												this
												.lotteryOrder.times).toFixed(2)
											item.notes = this.lotteryOrder.times
										}
									}
								} */
							})
						}
					}
					//将字符串转对象
					if (this.lotteryOrder.ballInfoList != null) {
						this.lotteryOrder.ballInfoList.map((item, idx) => {
							this.$set(this.lotteryOrder.ballInfoList[idx], "content", JSON.parse(item
								.content))
							//將比賽结果转换成数组，并返回
							if (item.award != null) {
								this.$set(this.lotteryOrder.ballInfoList[idx], "award", item.award.split(
									','))
							} else {
								//考虑比赛结果还没有出的话设置一个默认值，防止报错
								var moren = ["", "", "", "", ""]
								this.$set(this.lotteryOrder.ballInfoList[idx], "award", moren)
							}
							if (item.bonusOdds != null) {
								this.$set(this.lotteryOrder.ballInfoList[idx], "bonusOdds", item.bonusOdds
									.split(','))
							} else {
								//考虑比赛结果还没有出的话设置一个默认值，防止报错
								var moren = ["", "", "", "", ""]
								this.$set(this.lotteryOrder.ballInfoList[idx], "bonusOdds", moren)
							}
						})
					}
					//处理超过一定高度采用滚动条
					this.$nextTick(() => {
						if (document.querySelector(".make").clientHeight > 500) {
							document.querySelector(".make").setAttribute("class", "scheme")
						}
					})
					//处理排列3的组三和组六的字符串转数组
					if (this.lotteryOrder.type == "3" || this.lotteryOrder.type == "10") {
						this.lotteryOrder.recordList.map(item => {
							if (item.mode == "1" || item.mode == "2" || item.mode == "6") {
								item.individual = JSON.parse(item.individual)
							}
						})
					}
					//处理大乐透
					if (this.lotteryOrder.type == "8" || this.lotteryOrder.type == "11" || this.lotteryOrder
						.type == "12" || this.lotteryOrder.type == "13") {
						this.lotteryOrder.recordList.map(item => {
							console.log(item)
							item.ten = JSON.parse(item.ten)
							item.individual = JSON.parse(item.individual)
						})
					}
					if (this.lotteryOrder.type == 3 || this.lotteryOrder.type == 4 || this.lotteryOrder.type ==
						5 || this.lotteryOrder.type == 8 ||
						this.lotteryOrder.type == 10 ||
						this.lotteryOrder.type == 11 ||
						this.lotteryOrder.type == 12 ||
						this.lotteryOrder.type == 13
					) {
						this.lotteryOrder.recordList.forEach(item => {
							if (item.individual != null && Array.isArray(item.individual)) {
								item.individual.sort((a, b) => {
									if (a.isGallbladder === b.isGallbladder) {
										return a.num - b.num;
									}
									return a.isGallbladder ? -1 : 1;
								});
							}
							if (item.ten != null && Array.isArray(item.ten)) {
								item.ten.sort((a, b) => {
									if (a.isGallbladder === b.isGallbladder) {
										return a.num - b.num;
									}
									return a.isGallbladder ? -1 : 1;
								});
							}
						})
					}
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	.uni-input-placeholder {
		text-align: center;
		font-size: 26upx;
	}

	.selected_num {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-wrap: wrap;

		uni-view {
			width: 100%;
		}
	}

	.btn_expanded {
		width: 600rpx;
		display: flex;
		margin-top: 24rpx;
		margin-bottom: 32rpx;
		margin: auto;
		justify-content: center;
	}

	.toggle-button {
		font-family: PingFangSC-Regular;
		font-size: 26upx;
		color: #979797;
		letter-spacing: 0;
		font-weight: 400;
		margin-top: 24rpx;
		line-height: 48rpx;
	}

	.expanded {
		width: 40rpx;
		height: 40rpx;
		margin-top: 29.6rpx;
	}

	.expandedImg {
		width: 100%;
		height: 90%;
	}

	::v-deep .u-tag {
		margin-bottom: 5px;
	}

	.paiban {
		display: flex;
		align-items: center;
		justify-content: space-between;
		flex-direction: row;
		width: 100%;
	}

	::v-deep .u-popup .u-transition {
		z-index: 998 !important;
	}

	.content {
		display: inline-block;
		width: 58upx;
		height: 58upx;
		background-color: #FF3F43;
		border-radius: 50%;
		margin: 0px 4upx 4upx 0px;

		p {
			width: 58upx;
			height: 58upx;
			color: #fff;
			text-align: center;
			line-height: 58upx;
			font-size: 26upx;
		}
	}

	.rangqiuBlue {
		color: #2d8cf0 !important;
	}


	::v-deep .uicon-more-dot-fill {
		font-size: 42upx !important;
		color: #fff !important;
	}

	.scheme {
		margin-top: 30px;
		overflow: auto;
		height: 1000rpx;
		overflow-x: hidden;
	}

	::v-deep .uni-col {
		padding: 0px 0px 5px 0px !important;
		display: flex !important;
		justify-content: center !important;
		align-items: center !important;
	}

	.rangqiu {
		color: #FF3F43;
	}

	.box {
		background: #f7f9fa;

		.uni-body {
			color: #909399;
		}

		.info {
			background: radial-gradient(circle, rgba(63, 94, 251, 1) 0%, rgba(255, 63, 67, 1) 100%);
			color: #ffffff;
			display: flex;
			flex-direction: column;

			view {
				text-align: center;

				.font {
					font-size: 28rpx;
				}

				p {
					padding-bottom: 14rpx;
					font-size: 36rpx;
				}
			}
		}

		.body {
			margin-bottom: 110upx;

			.uni-card {
				margin: 0px !important;
				padding: 0px !important;
			}

			.title {
				font-size: 28rpx;
			}

			.tip {
				margin-top: 20rpx;

				view {
					margin-top: 20rpx;
				}
			}

			.phone {
				margin-top: 20rpx !important;
				height: 100%;
			}
		}
	}
</style>