<template>
	<!-- 购彩订单详情列表 -->
	<view class="box">
		<view class="info" style="background:#3999FE">
			<view style="display: flex;justify-content: space-around;align-items: center;padding-top: 40px">
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
			<view style="padding-top: 15px;"
				v-if="lotteryOrder.type=='0'||lotteryOrder.type=='1'||lotteryOrder.type=='2'||lotteryOrder.type=='9'">
				<u-button size="normal" shape="circle" customStyle="color:#000;width:92%;height:35px" color="#fff">
					预测最高奖金：<span style="color:#00C3FF">￥{{lotteryOrder.forecast}}</span>
				</u-button>
			</view>
		</view>
		<view class="body">
			<uni-card is-shadow v-if="lotteryOrder.type=='3'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 1;transform:rotate(10deg);">
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
						<uni-th width="50" align="center">期号</uni-th>
						<uni-th width="50" align="center">投注内容</uni-th>
						<uni-th width="50" align="center">投注方式</uni-th>
						<uni-th width="50" align="center">赛果</uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.recordList" :key="index">
						<uni-td width="50" align="center">{{record.stageNumber}}</uni-td>
						<uni-td width="50" align="center" style="word-wrap:break-word;word-break:break-all;">
							{{record.hundred}} <span v-if="record.hundred!=null">|</span> {{record.ten}}
							<span v-if="record.ten!=null">|</span>
							<span v-if="record.mode=='1'||record.mode=='2'" v-for="(c,index) in record.individual">
								{{c.num}}
								<span style="color:#00C3FF" v-if="c.isGallbladder">[胆]</span>
								<span v-if="index<record.individual.length-1">,</span>
							</span>
							<span v-if="record.mode!='1'&&record.mode!='2'">
								{{record.individual}}
							</span>
						</uni-td>
						<uni-td width="50" align="center">{{record.mode|formatMode}}</uni-td>
						<uni-td width="50" align="center">{{record.reward}}</uni-td>
					</uni-tr>
				</uni-table>

			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='4'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 1;transform:rotate(10deg);">
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
						<uni-th width="65" align="center">投注内容</uni-th>
						<uni-th width="65" align="center">赛果</uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.recordList" :key="index">
						<uni-td align="center">{{record.stageNumber}}</uni-td>
						<uni-td align="center" style="word-wrap:break-word;word-break:break-all;">{{record.myriad}} |
							{{record.kilo}} | {{record.hundred}} |
							{{record.ten}} |
							{{record.individual}}
						</uni-td>
						<uni-td align="center">{{record.reward}}</uni-td>
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='5'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 1;transform:rotate(10deg);">
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
						<uni-th width="65" align="center">投注内容</uni-th>
						<uni-th width="65" align="center">赛果</uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.recordList" :key="index">
						<uni-td align="center">{{record.stageNumber}}</uni-td>
						<uni-td align="center"
							style="word-wrap:break-word;word-break:break-all;">{{record.hundredMyriad}}
							|{{record.tenMyriad}} |{{record.myriad}} |
							{{record.kilo}} | {{record.hundred}} |
							{{record.ten}} |
							{{record.individual}}
						</uni-td>
						<uni-td align="center">{{record.reward}}</uni-td>
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='8'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 1;transform:rotate(10deg);">
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
						<uni-th width="65" align="center">投注内容</uni-th>
						<uni-th width="65" align="center">赛果</uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.recordList" :key="index">
						<uni-td align="center">{{record.stageNumber}}</uni-td>
						<uni-td align="center" style="word-wrap:break-word;word-break:break-all;">
							<span v-for="(c,index) in record.ten">
								{{c.num}}
								<span style="color:#00C3FF" v-if="c.isGallbladder">[胆]</span>
								<span v-if="index<record.ten.length-1">,</span>
							</span>
							<span style="padding: 0px 5px">|</span>
							<span v-for="(c,index) in record.individual">
								{{c.num}}
								<span style="color:#00C3FF" v-if="c.isGallbladder">[胆]</span>
								<span v-if="index<record.individual.length-1">,</span>
							</span>
						</uni-td>
						<uni-td align="center">{{record.reward}}</uni-td>
					</uni-tr>
				</uni-table>
			</uni-card>

			<uni-card is-shadow v-if="lotteryOrder.type=='0'">
				<view style="position: absolute;left: 8%;top:-2%;z-index: 1;transform:rotate(10deg);">
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
							<uni-th width="65" align="center">投注内容</uni-th>
							<uni-th width="50" align="center">赛果<br>(全/半)</uni-th>
						</uni-tr>
						<!-- 表格数据行 -->
						<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index"
							@click.native="handingCorrect(record.id,record.content)">
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
				<view style="position: absolute;left: 8%;top:-2%;z-index: 1;transform:rotate(10deg);">
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
							<uni-th width="75" align="center">投注内容</uni-th>
							<uni-th width="40" align="center">赛果</uni-th>
						</uni-tr>
						<!-- 表格数据行 -->
						<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index" @click.native="handingCorrect(record.id,record.content)">
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
				<view style="position: absolute;left: 8%;top:-2%;z-index: 1;transform:rotate(10deg);">
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
						<uni-th width="65" align="center">投注内容</uni-th>
						<uni-th width="50" align="center">赛果<br><span
								v-if="lotteryOrder.type=='2'">(全/半)</span></uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index" @click.native="handingCorrect(record.id,record.content)">
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
				<view style="position: absolute;left: 8%;top:-2%;z-index: 1;transform:rotate(10deg);">
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
						<uni-th width="65" align="center">投注内容</uni-th>
						<uni-th width="50" align="center">赛果<br></uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index" @click.native="handingCorrect(record.id,record.content)">
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
				<view style="position: absolute;left: 8%;top:-2%;z-index: 1;transform:rotate(10deg);">
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
						<uni-th width="65" align="center">投注内容</uni-th>
						<uni-th width="50" align="center">赛果</uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(record,index) in lotteryOrder.ballInfoList" :key="index" @click.native="handingCorrect(record.id,record.content)">
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

			<uni-card class="phone"
				v-if="lotteryOrder.schemeDetails!=null&&lotteryOrder.schemeDetails!=undefined&&lotteryOrder.schemeDetails!=''&&lotteryOrder.type!=3&&lotteryOrder.type!=4&&lotteryOrder.type!=5&&lotteryOrder.type!=8">
				<p>
					<view class="title" v-if="lotteryOrder.isOptimize=='1'">优化后方案详情</view>
					<view class="title" v-else>中奖方案详情</view>
					<uni-table border stripe emptyText="暂无更多数据" class="make">
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
								<uni-th align="center" width="80px">投注内容</uni-th>
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

			<uni-card class="phone"
				v-if="lotteryOrder.schemeDetails!=null&&lotteryOrder.schemeDetails!=undefined&&lotteryOrder.schemeDetails!=''&&lotteryOrder.type==3||lotteryOrder.type==4||lotteryOrder.type==5||lotteryOrder.type==8">
				<p>
					<view class="title">中奖方案详情</view>
					<uni-table border stripe emptyText="暂无更多数据" class="make">
						<!-- 表头行 -->
						<uni-tr>
							<uni-th width="20px" align="center">期号</uni-th>
							<uni-th align="center" width="30px" v-if="lotteryOrder.type==3">过关</uni-th>
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
				<view class="uni-body">订单编号：{{lotteryOrder.orderId}}</view>
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
		<u-popup :show="show">
			<view style="height: 500px;margin: 0 auto;overflow: auto;" v-if="lotteryOrder.type=='0'">
				<view v-if="content.notLetOddsList!=undefined&&content.notLetOddsList.length>0"
					style="margin-top: 10px;">
					<u-tag text="胜平负" bgColor="#fbb52f" borderColor="#fbb52f"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.notLetOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.letOddsList!=undefined&&content.letOddsList.length>0" style="margin-top: 10px;">
					<u-tag text="让球胜平负" bgColor="#3CC48D" borderColor="#3CC48D"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.letOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.goalOddsList!=undefined&&content.goalOddsList.length>0" style="margin-top: 10px;">
					<u-tag text="总进球" bgColor="#1296db" borderColor="#1296db"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.goalOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.halfWholeOddsList!=undefined&&content.halfWholeOddsList.length>0"
					style="margin-top: 10px;">
					<u-tag text="半全场" bgColor="#6b1cf2" borderColor="#6b1cf2"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.halfWholeOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.scoreOddsList!=undefined&&content.scoreOddsList.length>0" style="margin-top: 10px;">
					<u-tag text="比分" bgColor="#19be6b" borderColor="#19be6b"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.scoreOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
			</view>
			
			<view style="height: 500px;margin: 0 auto;overflow: auto;" v-if="lotteryOrder.type=='1'">
				<view v-if="content.winNegativeOddsList!=undefined&&content.winNegativeOddsList.length>0"
					style="margin-top: 10px;">
					<u-tag text="胜负" bgColor="#fbb52f" borderColor="#fbb52f"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.winNegativeOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.cedePointsOddsList!=undefined&&content.cedePointsOddsList.length>0" style="margin-top: 10px;">
					<u-tag text="让分胜负" bgColor="#3CC48D" borderColor="#3CC48D"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.cedePointsOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.sizeOddsList!=undefined&&content.sizeOddsList.length>0" style="margin-top: 10px;">
					<u-tag text="大小分" bgColor="#1296db" borderColor="#1296db"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.sizeOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.score" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.differenceOddsList!=undefined&&content.differenceOddsList.length>0"
					style="margin-top: 10px;">
					<u-tag text="胜负差" bgColor="#6b1cf2" borderColor="#6b1cf2"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.differenceOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
			</view>
			
			<view style="height: 500px;margin: 0 auto;overflow: auto;" v-if="lotteryOrder.type=='2'||lotteryOrder.type=='9'">
				<view v-if="content.letOddsList!=undefined&&content.letOddsList.length>0" style="margin-top: 10px;">
					<u-tag text="让球胜平负" bgColor="#3CC48D" borderColor="#3CC48D"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.letOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.oddEvenOdds!=undefined&&content.oddEvenOdds.length>0" style="margin-top: 10px;">
					<u-tag text="单双" bgColor="#3CC48D" borderColor="#3CC48D"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.oddEvenOdds" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.goalOddsList!=undefined&&content.goalOddsList.length>0" style="margin-top: 10px;">
					<u-tag text="总进球" bgColor="#1296db" borderColor="#1296db"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.goalOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.halfWholeOddsList!=undefined&&content.halfWholeOddsList.length>0"
					style="margin-top: 10px;">
					<u-tag text="半全场" bgColor="#6b1cf2" borderColor="#6b1cf2"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.halfWholeOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
				<view v-if="content.scoreOddsList!=undefined&&content.scoreOddsList.length>0" style="margin-top: 10px;">
					<u-tag text="比分" bgColor="#19be6b" borderColor="#19be6b"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.scoreOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
			</view>
			
			<view style="height: 500px;margin: 0 auto;overflow: auto;" v-if="lotteryOrder.type=='6'||lotteryOrder.type=='7'">
				<view v-if="content.notLetOddsList!=undefined&&content.notLetOddsList.length>0" style="margin-top: 10px;">
					<u-tag text="胜平负" bgColor="#3CC48D" borderColor="#3CC48D"
						style="display: flex;justify-content: center;align-items: center;"></u-tag>
					<u--form labelPosition="left">
						<view v-for="item in content.notLetOddsList" :key="item.id">
							<u-form-item label="投注" prop="userInfo.name" borderBottom ref="item1">
								<u-input v-model="item.describe" style="width: 80px"></u-input>
								<u-input v-model="item.odds" style="width: 80px"></u-input>
							</u-form-item>
						</view>
					</u--form>
				</view>
			</view>
			<view style="display: flex;justify-content: center;align-items: center;margin-bottom: 10px;">
				<u-button text="取消" customStyle="margin: 10px" @click="show=false"></u-button>
				<u-button color="#3999FE" text="确定" customStyle="margin: 10px" @click="updateDeposit"></u-button>
			</view>
		</u-popup>
	</view>
</template>

<script>
	import {
		getLotteryOrderById
	} from '@/api/lotteryOrder.js'
	import {
		correctOrderUpdate
	} from '@/api/correct.js'
	export default {
		data() {
			return {
				lotteryOrder: {},
				that: this,
				show: false,
				id: "",
				correctOrderUpdate: {
					id: "",
					orderId: "",
					footballMatch: null,
					basketballMatch: null,
					beiDanMatch: null,
					winBurdenMatch: null,
					type: "",
				},
				content: "",
			}
		},
		onLoad(option) {
			this.id = option.id;
			this.init(option.id);
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
				}
			}
		},
		methods: {
			updateDeposit() {
				if (this.lotteryOrder.type == "0") {
					this.correctOrderUpdate.footballMatch = this.content
				} else if (this.lotteryOrder.type == "1") {
					this.correctOrderUpdate.basketballMatch = this.content
				} else if (this.lotteryOrder.type == "2" || this.lotteryOrder.type == "9") {
					this.correctOrderUpdate.beiDanMatch = this.content
				} else if (this.lotteryOrder.type == "6" || this.lotteryOrder.type == "7") {
					this.correctOrderUpdate.winBurdenMatch = this.content
				}
				correctOrderUpdate(this.correctOrderUpdate).then(res => {
					if (res.success) {
						uni.showToast({
							title:"操作成功",
							icon:"success"
						})
						this.show = false;
						this.init(this.id);
					}
				})
			},
			handingCorrect(id, content) {
				if (this.lotteryOrder.state == '1') {
					this.content = content;
					this.show = true;
					this.correctOrderUpdate.id = id;
					this.correctOrderUpdate.orderId = this.lotteryOrder.id
					this.correctOrderUpdate.type = this.lotteryOrder.type
				}
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
				getLotteryOrderById(id).then(res => {
					this.lotteryOrder = res;
					if (res.schemeDetails != null && res.schemeDetails != undefined && res.schemeDetails != "") {
						this.lotteryOrder.schemeDetails = JSON.parse(this.lotteryOrder.schemeDetails)
						//过滤掉数字彩
						if (this.lotteryOrder.type != 3 && this.lotteryOrder.type != 4 && this.lotteryOrder.type !=
							5 && this.lotteryOrder.type != 8) {
							this.lotteryOrder.schemeDetails.map((item, index) => {
								if (this.lotteryOrder.isOptimize == "0") {
									item.forecastBonus = parseFloat((item.forecastBonus / item.notes) *
										this
										.lotteryOrder.times).toFixed(2)
									item.notes = this.lotteryOrder.times
								}
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
					if (this.lotteryOrder.type == "3") {
						this.lotteryOrder.recordList.map(item => {
							if (item.mode == "1" || item.mode == "2") {
								item.individual = JSON.parse(item.individual)
							}
						})
					}
					//处理大乐透
					if (this.lotteryOrder.type == "8") {
						this.lotteryOrder.recordList.map(item => {
							item.ten = JSON.parse(item.ten)
							item.individual = JSON.parse(item.individual)
						})
					}
				})
			}
		}
	}
</script>

<style scoped lang="scss">
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

	.content {
		width: 20px;
		height: 20px;
		background-color: #FF3F43;
		border-radius: 50%;

		p {
			width: 20px;
			height: 20px;
			color: #fff;
			text-align: center;
			line-height: 20px;
			font-size: 12px;
		}
	}

	.rangqiuBlue {
		color: #2d8cf0 !important;
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
			height: 320rpx;
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
					font-size: 38rpx;
				}
			}
		}

		.body {
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