<template>
	<div class="notice-list">
		<div class="nav-top clear">
			<el-button type="primary" plain size="small" icon="el-icon-back" style="float: left;" @click="$router.go(-1)">返回</el-button>
			<p>
				当前位置：
				<router-link to="/personCenter">个人中心</router-link>>
				<router-link to>会员卡详情</router-link>
			</p>
		</div>
		<h3>会员卡详情</h3>
		<div class="list">
			<div class="change-list">
				<!--<el-row class="item-title">
					<el-col :span="8">会员卡</el-col>
					<el-col :span="8">样式</el-col>
					<el-col :span="8">操作</el-col>
				</el-row>
				<el-row class="list-item" v-for="(item,index) in changeList" :key="index">
					<el-col :span="8">{{item.type}}</el-col>
					<el-col :span="8">
						<img :src='item.wenjian' style="width:150px;">
					</el-col>
					<el-col :span="8" class="number">
						<el-button type="text" @click="sequel(item.id)">续费</el-button>
						<el-button type="text" @click="abandon(item.id)">放弃</el-button>
					</el-col>
				</el-row>-->
				<el-row class="item-title">
					<el-col :span="4">会员卡</el-col>
					<el-col :span="4">样式</el-col>
					<el-col :span="4">到期日</el-col>
					<el-col :span="4">到期复投</el-col>
					<el-col :span="4">到期退出</el-col>
					<el-col :span="4">修改状态</el-col>
				</el-row>
				<el-row class="list-item" v-for="(item,index) in cardList" :key="index">
					<el-col :span="4">{{item.cardname}}</el-col>
					<el-col :span="4">
						<img :src='item.wenjian' style="width:150px;">
					</el-col>
					<el-col :span="4">{{item.endtime}}</el-col>
					<el-col :span="4">
						<!--<el-radio v-model="radio" label="复投"></el-radio>-->
						<div>
							<img v-if='item.is_withdrawal == 1' style='width: 18px;height: 18px;' src="../assets/images/radio_checked.png" />
							<img v-else style='width: 18px;height: 18px;' src="../assets/images/radio_no.png" />
						</div>
					</el-col>
					<el-col :span="4">
						<div>
							<img v-if='item.is_withdrawal == 2' style='width: 18px;height: 18px;' src="../assets/images/radio_checked.png" />
							<img v-else style='width: 18px;height: 18px;' src="../assets/images/radio_no.png" />
						</div>
						<!--<el-radio v-model="radio" label="退出"></el-radio>-->
					</el-col>
					<el-col :span="4" class="number">
						<el-button type="text" @click="modifyStatus(item)">修改</el-button>
					</el-col>
				</el-row>
			</div>
			<div class="block">
				<!--<el-button class='allAnandon' @click="allAnandon">全部放弃</el-button>-->
				<el-pagination class='pagination' @current-change="handleCurrentChange" :current-page="page" layout="total, prev, pager, next, jumper" :total="total"></el-pagination>
				<!--<el-button class='allLingqu' @click="allLingqu">全部领取</el-button>-->
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		name: "notice-list",
		data() {
			return {
				page: 1,
				size: 10,
				total: 0,
				changeList: [],
				idArr: [],
				cardList: [],
				radio: '1',
				status: ''
			};
		},
		filters: {

		},
		mounted() {
			//			this.getList();
			this.getVipCard()
		},
		methods: {
			modifyStatus(val) {
				var that = this
				console.log(val)
				//val.is_withdrawal = 1
				//				val.is_withdrawal == 2 ? status = 1 : status = 2
				if(val.is_withdrawal == 2) {
					that.status = 1
				} else {
					that.status = 2
				}
				console.log(that.status)
				that.$axios
					.post("card/repeatCard", {
						token: sessionStorage.getItem("token"),
						id: val.id,
						status: that.ststus
					})
					.then(res => {
						if(res.data.sta == 401) {
							this.$message.error("请重新登录！");
							this.$router.push("/login");

						}
						if(res.data.sta == 200) {
							//							this.info.qiandaoStatus = 1;
							that.$message({
								message: "修改成功！",
								type: "success"
							});
							that.getVipCard()
						} else {
							that.$message({
								message: res.data.msg,
								type: "error"
							});

						}
					});
			},
			handleSizeChange(size) {
				this.size = size;
			},
			handleCurrentChange(page) {
				this.page = page;
				//				this.getList();
				this.getVipCard()
			},
			getVipCard() {
				this.$axios
					.post("card/getCardList", {
						page: this.page3,
						token: sessionStorage.getItem("token")
					})
					.then(res => {
						if(res.data.sta == 401) {
							sessionStorage.removeItem("user");
							sessionStorage.removeItem("token");
							return;
						}
						if(res.data) {
							this.cardList = res.data.data.data;
							//							this.total3 = res.data.data.total;
							//							this.diyaTotal = res.data.znum;
							//							this.total4 = res.data.jnum;
						}
					});
			},
			//			sequel(val) {
			//				this.$axios
			//					.get("card/renewCard", {
			//						id: val,
			//						token: sessionStorage.getItem("token")
			//					})
			//					.then(res => {
			//						//console.log(res)
			//						if(res.data.sta == 401) {
			//							this.$message.error("请重新登录！");
			//							this.$router.push("/login");
			//						} else if(res.data.sta == 200) {
			//							this.$message.success('会员卡续费成功')
			//							this.getList()
			//						} else {
			//							this.$message.error(res.data.msg);
			//						}
			//					});
			//			},
			//			abandon(val) {
			//				this.$axios
			//					.get("card/giveupCard", {
			//						id: val,
			//						token: sessionStorage.getItem("token")
			//					})
			//					.then(res => {
			//						//console.log(res)
			//						if(res.data.sta == 401) {
			//							this.$message.error("请重新登录！");
			//							this.$router.push("/login");
			//						} else if(res.data.sta == 200) {
			//							this.$message.success('会员卡成功放弃')
			//							this.getList()
			//						} else {
			//							this.$message.error(res.data.msg);
			//						}
			//					});
			//			},
			//			allAnandon() {
			//				var that = this
			//				this.$axios
			//					.get("card/giveupCard", {
			//						//						id: that.idArr.join(),
			//						token: sessionStorage.getItem("token"),
			//						id: '',
			//						num: this.$route.query.num
			//					})
			//					.then(res => {
			//						//						//console.log(res)
			//						if(res.data.sta == 401) {
			//							this.$message.error("请重新登录！");
			//							this.$router.push("/login");
			//						} else if(res.data.sta == 200) {
			//							that.$message({
			//								message: '全放弃成功！',
			//								type: "success"
			//							});
			//							that.getList()
			//						} else {
			//							this.$message.success(res.data.msg)
			//						}
			//					});
			//			},
			//			allLingqu() {
			//				var that = this
			//				this.$axios
			//					.get("card/renewCard", {
			//						//						id: that.idArr.join(),
			//						token: sessionStorage.getItem("token"),
			//						id: '',
			//						num: this.$route.query.num
			//					})
			//					.then(res => {
			//						if(res.data.sta == 401) {
			//							this.$message.error("请重新登录！");
			//							this.$router.push("/login");
			//						} else if(res.data.sta == 200) {
			//							this.$message.error("全部领取成功！");
			//							that.getList()
			//						} else {
			//							this.$message.success(res.data.msg)
			//						}
			//					});
			//			},
			//			getList() {
			//				var that = this
			//				////console.log(this.$route.query.num, 'a')
			//				this.$axios
			//					.get("card/expireCardsList", {
			//						num: this.$route.query.num,
			//						token: sessionStorage.getItem("token")
			//					})
			//					.then(res => {
			//						console.log(res)
			//						if(res.data.sta == 401) {
			//							this.$message.error("请重新登录！");
			//							this.$router.push("/login");
			//						}
			//						if(res.data.data) {
			//							that.changeList = res.data.data.data;
			//							that.total = res.data.data.total;
			//							that.idArr = []
			//							for(var i = 0; i < res.data.data.data.length; i++) {
			//								that.idArr.push(res.data.data.data[i].id)
			//							}
			//						}
			//					});
			//			},
		}
	};
</script>

<style lang="scss" scoped>
	.notice-list {
		margin: 75px auto 20px;
		max-width: 960px;
		.nav-top {
			p {
				float: right;
				font-size: 14px;
				a {
					color: #409eff;
				}
			}
		}
		h3 {
			font-size: 30px;
			color: #303133;
			text-align: center;
			margin: 20px 0;
		}
		.list {
			padding: 20px 0 20px;
			box-shadow: 0 0 5px 0 rgba(0, 0, 0, 0.16);
			border-radius: 3px;
			.list-title {
				padding: 0 20px 10px;
				border-bottom: 1px solid #ddd;
				h4 {
					margin: 0;
					font-size: 16px;
				}
				p {
					font-size: 12px;
				}
			}
			.change-list {
				padding: 10px 20px;
				word-wrap: break-word;
				.item-title {
					line-height: 40px;
					margin: 10px 0 0 0;
					text-align: center;
					color: #999;
					font-size: 12px;
				}
				.list-item {
					font-size: 12px;
					color: #5d6b6e;
					text-align: center;
					// margin: 8px 0;
					line-height: 40px;
					border-top: 1px solid #dcdfe6;
					.number {
						color: #f1a90a;
						cursor: pointer;
					}
				}
			}
			.block {
				text-align: center;
				margin-top: 10px;
				display: flex;
				padding: 0 50px 0 126px;
				box-sizing: border-box;
				.allAnandon,
				.pagination,
				.allLingqu {
					display: inline-block;
				}
				.pagination {
					margin: 0 130px 0 130px;
				}
				.allLingqu {
					float: right;
				}
			}
		}
	}
</style>