<template>
	<div class="container">
		<div class="timeList clearfix">
		<div id="time">今天是：{{month}}月{{date}}日，{{week}}</div>
		<dl class="date-list">
			<dt class="currWeek">{{firstDayFormat}}-{{lastDayFormat}}</dt>
			<div class="list">
				<dd @click="firstWeek" id="firstWeek">{{firstDayFormat}}-{{lastDayFormat}}</dd>
				<dd @click="nextWeek(lastDay)" id="secondWeek">{{firstNextDayFormat}}-{{lastNextDayFormat}}</dd>
				<dd @click="nextWeek(lastNext)" id="threeWeek">{{threeDayFormat}}-{{threeLastDayFormat}}</dd>
			</div>
		</dl>
	</div>
	<div class="one-weeks">
			<ul class="clearfix date-weeks">
			</ul>
		</div>
	</div>
	
</template>
<script type="text/javascript">import $ from 'jquery';
export default {
	data() {
		return {
			month: '',
			date: '',
			week: '',
			weeklist: false,
			aryDay: '', //显示星期
			lastDay: '', //第一周最后一天
			firstDay: '', //第一周第一天
			lastDayFormat: '', //第一周最后一天格式化
			firstDayFormat: '', //第一周第一天格式化
			firstNext: '', //第二周第一天
			lastNext: '', //第二周最后一天
			lastNextDayFormat: '', //第二周最后一天格式化
			firstNextDayFormat: '', //第二周第一天格式化
			threeDay: '', //第三周第一天
			threeLast: '', //第三周最后一天
			threeDayFormat: '', //第三周第一天
			threeLastDayFormat: '', //第三周第二天
			lastNextDay: '',
			firstNextDay: '',
			onDay: '', //一天
			curr: ''
		};
	},
	mounted() {
		this.$nextTick(() => {
			this.sysTime();
			this.initDate();
			this.firstWeek();
			this.domHandle();
		});
	},
	methods: {
		sysTime() {
			var curr = new Date(); //获取日期对象
			this.month = curr.getMonth() + 1; //获得月份
			this.date = curr.getDate(); //获得日
			var day = curr.getDay(); //获得星期，day代表的是本周的第几天[0,6]
			var arr_week = new Array("星期日", "星期一", "星期二", "星期三", "星期四", "星期五", "星期六");
			this.week = arr_week[day];
		},
		domHandle() {
			$(".currWeek").on("click",function(){
				if($(".list").is("visible")){
					$(".list").hide();
				}else{
					$(".list").show();
				}
			});
			$(".list").on("click","dd",function(){
				$(".currWeek").text($(this).text());
				$(".list").hide();
			});
		},
		firstWeek() {
			this.curr = new Date();
			var weeks, month, dates;
			this.aryDay = new Array("日", "一", "二", "三", "四", "五", "六");
			$(".date-weeks").html("");
			for(var i = 0; i < 7; i++) {
				this.onDay = this.getDays()[i];
				weeks = this.onDay.getDay(); //获取每一天是星期几
				month = this.onDay.getMonth() + 1;
				dates = this.onDay.getDate();
				$(".date-weeks").append('<li class="one-day">' +
					'<span class="weeks">周' + this.aryDay[weeks] + '</span>' +
					'<span class="date">' + month + '.' + dates + '</span>' +
					'</li>');
				if(this.onDay.toLocaleDateString() == this.curr.toLocaleDateString()) {
					$(".date-weeks").find(".one-day").eq(i).css("background-color", "#f00")
				}
			}
		},
		//第二周 三周
		nextWeek(day) {
			this.curr = new Date();
			var weeks, month, dates;
			this.aryDay = new Array("日", "一", "二", "三", "四", "五", "六");
			$(".date-weeks").html("");
			for(var i = 0; i < 7; i++) {
				this.onDay = this.getNextWeekDatas(day)[i];
				weeks = this.onDay.getDay(); //获取每一天是星期几
				month = this.onDay.getMonth() + 1;
				dates = this.onDay.getDate();
				$(".date-weeks").append('<li class="one-day">' +
					'<span class="weeks">周' + this.aryDay[weeks] + '</span>' +
					'<span class="date">' + month + '.' + dates + '</span>' +
					'</li>')
			}
		},

		initDate() {
			this.firstDay = this.getDays()[0];
			this.lastDay = this.getDays()[6];
			this.firstDayFormat = (this.firstDay.getMonth() + 1) + '月' + this.firstDay.getDate() + '日'; //本周的第一天
			this.lastDayFormat = (this.lastDay.getMonth() + 1) + '月' + this.lastDay.getDate() + '日'; //本周的最后一天

			this.firstNext = this.getNextWeekDatas(this.lastDay)[0];
			this.lastNext = this.getNextWeekDatas(this.lastDay)[6];
			this.firstNextDayFormat = (this.firstNext.getMonth() + 1) + '月' + this.firstNext.getDate() + '日'; //本周的第一天
			this.lastNextDayFormat = (this.lastNext.getMonth() + 1) + '月' + this.lastNext.getDate() + '日'; //本周的最后一天

			this.threeDay = this.getNextWeekDatas(this.lastNext)[0];
			this.threeLast = this.getNextWeekDatas(this.lastNext)[6];
			this.threeDayFormat = (this.threeDay.getMonth() + 1) + '月' + this.threeDay.getDate() + '日'; //本周的第一天
			this.threeLastDayFormat = (this.threeLast.getMonth() + 1) + '月' + this.threeLast.getDate() + '日'; //本周的最后一天
		},
		//取得当前日期一周内的某一天
		getWeek(i) {
			var now = new Date();
			var n = now.getDay();
			var start = new Date();
			start.setDate(now.getDate() - n + i); //取得一周内的第一天、第二天、第三天...
			return start;
		},
		//取得当前日期一周内的七天
		getDays() {
			var days = new Array();
			for(var i = 1; i <= 7; i++) {
				days[i - 1] = this.getWeek(i);
			}
			return days;
		},
		//取得下一周的日期数(共七天)
		getNextWeekDatas(ndt) {
			var days = new Array();
			for(var i = 1; i <= 7; i++) {
				var dt = new Date(ndt);
				days[i - 1] = this.getNextWeek(dt, i);
			}
			return days;
		},
		//指定日期的下一周(后七天)
		getNextWeek(dt, i) {
			var today = dt;
			today.setDate(today.getDate() + i);
			return today;
		},
	}
}</script>
<style type="text/css">* {
	font-size: 14px;
}

.clearfix {
	display: table;
	content: '';
}

.clearfix:after {
	clear: both
}

.timeList {
	width: 100%;
	padding: 10px;
}

#time {
	float: left;
	height: 30px;
	line-height: 30px;
}

.date-list {
	position: relative;
	float: right;
	width: 150px;
	height: 30px;
	line-height: 30px;
	text-align: center;
}

.date-list .list {
	position: absolute;
	width: 150px;
	height: auto;
	z-index: 3;
	top: 30px;
	left: 0;
	border: 1px solid #ccc;
	border-radius: 6px;
	background-color: #FFFFFF;
}

.date-list .list dd {
	height: 30px;
	line-height: 30px;
}

.date-list .list dd:active {
	background-color: #EEEEEE;
}

.one-day {
	float: left;
	width: 14%;
	text-align: center;
}</style>