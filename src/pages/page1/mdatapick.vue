<template>
	<div class="m_calender">
		<input type="text" class="date_input" readonly="readonly" v-model="dataMsg" @click="toggleShow">

		<div class="datamain" v-if="open">
			<div class="datacon">
				<div class="con">
					<div class="date_main">
						<div class="title">
							<div class="time">开始日期</div>
							<div class="timenum">{{start}}</div>
						</div>
						<div class="date_top">
							<em class="prev_month hoverhands" @click="prev('start')"></em>
							<p>
								<span class="top_month">{{getMonthText(startMonth)}}</span>
								<span class="top_year">{{startYear}}</span>
							</p>
							<em v-if="startNext" class="next_month hoverhands" @click="next('start')"></em>
						</div>
						<div class="date_week">
							<em>周日</em>
							<em>周一</em>
							<em>周二</em>
							<em>周三</em>
							<em>周四</em>
							<em>周五</em>
							<em>周六</em>
						</div>
						<div class="date_day">
							<em v-for="s in startlist" :class="s.className" @click="clickDate(s,'start')">{{ s.num }}</em>
						</div>
					</div>
				</div>
				<div class="con">
					<div class="date_main">
						<div class="title">
							<div class="time">结束日期</div>
							<div class="timenum">{{end}}</div>
						</div>
						<div class="date_top">
							<em v-if="endPrev" class="prev_month hoverhands" @click="prev('end')"></em>
							<p>
								<span class="top_month">{{getMonthText(endMonth)}}</span>
								<span class="top_year">{{endYear}}</span>
							</p>
							<em class="next_month hoverhands" @click="next('end')"></em>
						</div>
						<div class="date_week">
							<em>周日</em>
							<em>周一</em>
							<em>周二</em>
							<em>周三</em>
							<em>周四</em>
							<em>周五</em>
							<em>周六</em>
						</div>
						<div class="date_day">
							<em v-for="s in endlist" :class="s.className" @click="clickDate(s,'end')">{{ s.num }}</em>
						</div>
					</div>
				</div>
			</div>
			<div class="databtn">
				<div class="btn hoverhands" @click="toggleShow">取消</div>
				<div class="btn active hoverhands" @click="Determine">确定</div>
			</div>
		</div>

	</div>
</template>

<script>
	export default {
		props: ["child"],
		data: function() {
			return {
				open: false,

				start: this.child.start,
				startYear: '',
				startMonth: '',
				startFirstWeek: '',
				startDaySum: '',
				startlist: [],
				startNext:true,

				end: this.child.end,
				endYear: '',
				endMonth: '',
				endFirstWeek: '',
				endDaySum: '',
				endlist: [],
				endPrev:true
			}
		},
		computed: {
			activeStartData() {
				return new Date(this.start).getTime();
			},
			activeEndData() {
				return new Date(this.end).getTime();
			},
			dataMsg() {
				if(this.child) {
					return `${this.child.start} ~ ${this.child.end}`
				}
			},
			check(tag) {
				var startTime = new Date(`${this.startYear}-${this.checkVal(this.startMonth)}-01`).getTime();
				var endTime = new Date(`${this.endYear}-${this.checkVal(this.endMonth)}-01`).getTime();
				if(endTime <= startTime) {
					return false;
				}
				return true;
			}
		},
		mounted() {
			var startDate = new Date(this.start);
			this.startYear = startDate.getFullYear();
			this.startMonth = startDate.getMonth() + 1;

			var endDate = new Date(this.end);
			this.endYear = endDate.getFullYear();
			this.endMonth = endDate.getMonth() + 1;

			this.init();
		},
		methods: {
			toggleShow() {
				this.open = !this.open;
			},
			init() {
				this.startFirstWeek = new Date(`${this.startYear}-${this.checkVal(this.startMonth)}-01`).getDay();
				this.startDaySum = this.getMonthDaySum(`${this.startYear}-${this.checkVal(this.startMonth)}-01`);
				this.startlist = this.initDate(this.startFirstWeek, this.startDaySum, this.startYear, this.startMonth, this.activeStartData);

				this.endFirstWeek = new Date(`${this.endYear}-${this.checkVal(this.endMonth)}-01`).getDay();
				this.endDaySum = this.getMonthDaySum(`${this.endYear}-${this.checkVal(this.endMonth)}-1`);
				this.endlist = this.initDate(this.endFirstWeek, this.endDaySum, this.endYear, this.endMonth, this.activeEndData)
			},
			prev(tag) {
				if(tag == 'end') {
					if(!this.check) {
						return false;
					}
				}
				this[tag + 'Month']--;
				if(!this[tag + 'Month']) {
					this[tag + 'Month'] = 12;
					this[tag + 'Year']--;
				}
				this.$nextTick(() => {
					this.init();
				})
			},
			next(tag) {
				if(tag == 'start') {
					if(!this.check) {
						return false;
					}
				}
				this[tag + 'Month']++;
				if(this[tag + 'Month'] > 12) {
					this[tag + 'Month'] = 1;
					this[tag + 'Year']++;
				}
				this.$nextTick(() => {
					this.init();
				})
			},
			getMonthDaySum(val) { //获取当前月有多少天
				var curDate = new Date(val);
				var curMonth = curDate.getMonth();
				curDate.setMonth(curMonth + 1);
				curDate.setDate(0);
				return curDate.getDate();
			},
			initDate(FirstWeek, DaySum, Year, Month, activeData) {
				var list = [];
				for(var i = 1; i <= FirstWeek; i++) {
					var Dates = new Date(`${Year}-${this.checkVal(Month)}-01`);
					Dates.setDate(i - FirstWeek);
					list.push({
						"className": "disabled",
						"num": Dates.getDate(),
						"date": `${Year}-${this.checkVal(Month-1)}-${Dates.getDate()}`
					})
				}
				for(var i = 1; i <= DaySum; i++) {
					var isActive = new Date(`${Year}-${this.checkVal(Month)}-${this.checkVal(i)}`).getTime() == activeData;
					list.push({
						"className": isActive ? 'on' : '',
						"num": i,
						"date": `${Year}-${this.checkVal(Month)}-${this.checkVal(i)}`
					})
				}

				var nextData = new Date(`${Year}-${this.checkVal(Month)}-${DaySum}`).getDay();
				for(var i = 1; i <= 6 - nextData; i++) {
					list.push({
						"className": "disabled",
						"num": i,
						"date": `${Year}-${this.checkVal(Month+1)}-${this.checkVal(i)}`
					})
				}
				return list;

			},
			checkVal(num) {
				num = +num;
				return +num < 10 ? "0" + num : '' + num;
			},
			clickDate(val, tag) {
				if(val.className !== 'disabled') {
					this[tag] = val.date;
					this.$nextTick(() => {
						this.init();
					})
				}
			},
			Determine() {
				this.toggleShow();
				this.$emit("cllback", this.start, this.end)
			},
			getMonthText(m) {
				return ['', '一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月'][m]
			}
		}

	}
</script>
<style lang="less" scoped>
	.m_calender {
		position: relative;
		display: block;
		margin: 0 auto;
		.date_input {
			margin: 0 auto;
			width: 200px;
			border: 1px solid red;
			text-align: center;
			height: 20px;
			line-height: 20px;
			cursor: pointer;
			display: block;
			color: #484848;
			font-size: 12px;
		}
		.datamain {
			border: 1px solid #E6E6E6;
			position: absolute;
			left: 50%;
			top: 30px;
			transform: translateX(-50%);
			&::before {
				content: '';
				position: absolute;
				left: 50%;
				transform: translateX(-50%);
				top: -18px;
				width: 0;
				height: 0;
				border-top: 9px solid rgba(0, 0, 0, 0);
				border-right: 9px solid rgba(0, 0, 0, 0);
				border-bottom: 9px solid #E6E6E6;
				border-left: 9px solid rgba(0, 0, 0, 0);
			}
			&::after {
				content: '';
				position: absolute;
				left: 50%;
				transform: translateX(-50%);
				top: -15px;
				width: 0;
				height: 0;
				border-top: 8px solid rgba(0, 0, 0, 0);
				border-right: 8px solid rgba(0, 0, 0, 0);
				border-bottom: 8px solid white;
				border-left: 8px solid rgba(0, 0, 0, 0);
			}
			.datacon {
				display: flex;
				.con {
					margin: 5px 10px;
					.date_main {
						width: 232px;
						background: #FFFFFF;
						z-index: 20;
						.title {
							padding: 8px 4px;
							display: flex;
							justify-content: space-between;
							align-items: center;
							font-size: 14px;
							.time {
								color: #939393;
							}
							.timenum {
								color: #333333;
							}
						}
						.date_top {
							height: 28px;
							line-height: 28px;
							padding: 0 6px;
							display: flex;
							justify-content: space-around;
							align-items: center;
							em {
								width: 14px;
								font-style: normal;
								margin: 9px 4px;
								&.prev_month {
									width: 0px;
									height: 0px;
									border-top: 7px solid rgba(0, 0, 0, 0);
									border-bottom: 7px solid rgba(0, 0, 0, 0);
									border-right: 7px solid #A1A1A1;
									border-left: 7px solid rgba(0, 0, 0, 0);
								}
								&.next_month {
									width: 0px;
									height: 0px;
									border-top: 7px solid rgba(0, 0, 0, 0);
									border-bottom: 7px solid rgba(0, 0, 0, 0);
									border-left: 7px solid #A1A1A1;
									border-right: 7px solid rgba(0, 0, 0, 0);
								}
							}
							p {
								width: 100px;
								flex: 1;
								font-size: 12px;
								font-weight: 700;
								text-align: center;
								span {
									width: 40px;
									display: inline-block;
									text-align: center;
								}
							}
						}
						.date_week {
							height: 24px;
							em {
								box-sizing: border-box;
								font-style: normal;
								width: 33px;
								height: 24px;
								line-height: 24px;
								display: block;
								float: left;
								color: #484848;
								font-size: 12px;
								text-align: center;
							}
						}
						.date_day {
							overflow: hidden;
							border: 1px solid #E6E6E6;
							em {
								font-style: normal;
								width: 32px;
								height: 20px;
								line-height: 20px;
								display: block;
								float: left;
								color: #484848;
								font-size: 12px;
								text-align: center;
								border-right: 1px solid #E6E6E6;
								cursor: pointer;
								&:nth-child(7n) {
									border-right: 0px solid #E6E6E6;
								}
								&.on {
									color: #FFFFFF;
									background-color: #E76E00;
								}
								&.disabled {
									color: #CCCCCC;
									background-color: #FFFFFF;
									cursor: default;
								}
							}
						}
					}
				}
			}
			.databtn {
				text-align: right;
				display: flex;
				justify-content: flex-end;
				align-items: center;
				padding: 5px 0px 11px;
				.btn {
					display: flex;
					justify-content: center;
					align-items: center;
					box-sizing: border-box;
					width: 56px;
					height: 24px;
					border: 1px solid red;
					font-size: 12px;
					border: 1px solid #E6E6E6;
					border-radius: 2px;
					&.active {
						color: wheat;
						margin: 0px 8px;
						background: #EE6E25;
					}
				}
			}
		}
	}
</style>