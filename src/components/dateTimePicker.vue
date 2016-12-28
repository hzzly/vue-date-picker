<template>
  <div class="picker-box">
    <div class="picker-header">
        <div class="picker-year" id="picker-year">{{year}}年</div>
        <div class="picker-month" id="picker-month">{{month}}月</div>
        <div class="picker-prev" id="picker-prev" @click="preMon"> << </div>
        <div class="picker-next" id="picker-next" @click="nextMon"> >> </div>
    </div>
    <div class="picker-content">
        <div class="picker-week" style="clear: both;">
            <div class="picker-weekday">日</div>
            <div class="picker-weekday">一</div>
            <div class="picker-weekday">二</div>
            <div class="picker-weekday">三</div>
            <div class="picker-weekday">四</div>
            <div class="picker-weekday">五</div>
            <div class="picker-weekday">六</div>
        </div>
        <div class="picker-con" style="clear: both;">
            <div class="picker-day" v-for="pick in picker" :class="{'outfocus': pick.outfocus, 'today': pick.showToday, 'start': showStartEnvfun(pick.dateNum,pick.outfocus), 'end': showEndEnvfun(pick.dateNum,pick.outfocus), 'black': showBlack(pick.dateNum,pick.outfocus), 'half': showHalffun(pick.dateNum,pick.outfocus)}" @click="checkDayEnv(pick.dateNum,pick.outfocus)">{{pick.dateNum}}</div>
        </div>
    </div>
    <div class="picker-footer" style="clear: both;">
        <div class="picker-button-today"><span></span>今天</div>
        <div class="picker-button-start"><span></span>开工</div>
        <div class="picker-button-end"><span></span>完工</div>
    </div>
    <div class="confim" @click="confimDate()">确定</div>
  </div>
</template>

<script>
export default {
  name: 'picker-box',
  data () {
    return {
      year: '',
      month: '',
      picker: null,
      firstDay: '', //每个月第一天星期几
      endDay: '', 
      nextMonLen: '',
      toDay: '',  //今天
      toDayIndex: 0,
      startEnv: '', //开始日期
      endEnv: '',  //结束日期
      show: false,
      showToday: false,
      showStartEnv: false,
      showEndEnv: false
    }
  },
  created() {
    var date = new Date();
    this.toDay = date.getDate();
    this.year = date.getFullYear();
    this.month = date.getMonth() + 1;
    sessionStorage.year = this.year;
    sessionStorage.month = this.month;
    sessionStorage.toDay = this.toDay;
    sessionStorage.num = 0;
    sessionStorage.notClickLenDay = 0;       
    var workStartDate = sessionStorage.workStartDate;
    var workEndDate = sessionStorage.workEndDate;
    if (workStartDate && workEndDate) {
      var workStartDateArr = workStartDate.split("-");
      var workStartYear = workStartDateArr[0];
      var workStartMonth = workStartDateArr[1];
      this.year = workStartYear;
      this.month = workStartMonth;
      this.createCalendar(this.year, this.month);
      this.startEnv = workStartDate;
      this.endEnv = workEndDate;
    } else {
      this.createCalendar(this.year, this.month);
      this.startEnv = this.getTwoDay(this.year + '-' + this.month + '-' + this.toDay);
      this.endEnv = this.getSixDay(this.year + '-' + this.month + '-' + this.toDay);
    }

    console.log(this.startEnv);
    console.log(this.endEnv);
  },
  methods: {
    createCalendar: function (year, month) {
      this.clearCalendar();
      if (this.show) {

      }
      if (sessionStorage.year == this.year && sessionStorage.month == this.month) {
        this.show = true;
      } else {
        this.show = false;
      }
      month = parseInt(month, 10);
      var d = new Date(year, month - 1, 0);
      var lastMonthlastDay = d.getDate();

      this.firstDay = this.getFirstDay(year, month);

      var lastMon = [];
      var currentMon = [];
      //求上个月剩余多少天显示在本月
      for (var i = 0; i < this.firstDay; i++) {
        var day = lastMonthlastDay--;
        lastMon.push({
          dateNum: day,
          outfocus: true
        });
      }
      var lastMon = lastMon.reverse();
      var monthLen = this.getMonthLen(year, month);
      this.endDay = lastMon.length + monthLen;
      //本月天数
      for (var i = 1; i <= monthLen; i++) {
        if (this.show) {
          if (this.toDay === i) {
            this.toDayIndex = i + lastMon.length - 1;
            this.showToday = true;
          } else {
            this.showToday = false;
          }

        }
        if (this.show && this.toDay > i) {
          currentMon.push({
            dateNum: i,
            outfocus: true,
            showToday: this.showToday
          });
        } else {
          currentMon.push({
            dateNum: i,
            outfocus: false,
            showToday: this.showToday
          });
        }

      }
      var c = lastMon.concat(currentMon);
      this.nextMonLen = 42 - c.length;
      //下月天数显示在本月
      for (var i = 1; i <= this.nextMonLen; i++) {
        c.push({
          dateNum: i,
          outfocus: true
        });
      }
      var lastMon = [];
      var currentMon = [];
      this.picker = c;

    },
    clearCalendar: function () {
      this.picker = [];
    },
    //获得每个月第一天星期几   0：星期日，1：星期一
    getFirstDay: function (year, month) {
      var firstDay = new Date(year, month - 1, 1);
      return firstDay.getDay();
    },
    //获得每个月的天数
    getMonthLen: function (year, month) {
      var nextMonth = new Date(year, month, 1);
      nextMonth.setHours(nextMonth.getHours() - 1);
      return nextMonth.getDate();
    },
    preMon: function () {
      //本月以前不能点
      if (this.year == sessionStorage.year && this.month <= sessionStorage.month) {
        return;
      }

      this.month -= 1;
      if (this.month < 1) {
        this.year -= 1;
        this.month = 12;
      }
      this.createCalendar(this.year, this.month);
    },
    nextMon: function () {
      this.month += 1;

      if (this.month > 12) {
        this.year = parseInt(this.year) + 1;
        this.month = 1;
      }

      this.createCalendar(this.year, this.month);

    },
    checkDayEnv: function (dateNum, outfocus, index) {
      if (!outfocus) {
        var check_day = this.year + '-' + this.month + '-' + dateNum;
        if (this.dateCompare(this.endEnv, check_day) == 0) { //开工后
          console.log('完工后');
          this.endEnv = check_day;
          this.showEndEnvfun(dateNum);
        } else if (this.dateCompare(this.endEnv, check_day) == 3) {  //点完工当天
          console.log('完工当天');
          this.startEnv = check_day;
          this.showHalffun(dateNum)
        } else if (this.dateCompare(this.startEnv, check_day) == 3) {  //点开工当天
          console.log('开工当天');
          this.endEnv = check_day;
          this.showHalffun(dateNum);
        } else if (this.dateCompare(this.startEnv, check_day) == 1) {
          console.log('开工前');
          this.startEnv = check_day;
          this.showStartEnvfun(dateNum);
        } else if (this.dateCompare(this.startEnv, check_day) == 0 && this.dateCompare(this.endEnv, check_day) == 1) {
          console.log('开工之间');
          var disStartEnvLen = this.getDatePeriod(this.startEnv, check_day) - 1;
        // alert(disStartEnvLen)
          var disSEndEnvLen = this.getDatePeriod(this.endEnv, check_day) - 1;
          if (disStartEnvLen > disSEndEnvLen) {
            this.endEnv = check_day;
            this.showEndEnvfun(dateNum);
          } else {
            this.startEnv = check_day;
            this.showStartEnvfun(dateNum);
          }
        }
      }
    },
    //两个日期之间间隔多少天
    getDatePeriod: function (sDate1, sDate2) {
      var aDate, oDate1, oDate2, iDays;

      aDate = sDate1.split("-");
      oDate1 = new Date(aDate[1] + '/' + aDate[2] + '/' + aDate[0]); //转换为12-18-2016格式
      aDate = sDate2.split("-");
      oDate2 = new Date(aDate[1] + '/' + aDate[2] + '/' + aDate[0]);

      iDays = parseInt(Math.abs(oDate1 - oDate2) / 1000 / 60 / 60 / 24); //把相差的毫秒数转换为天数
      return iDays;
    },
    //计算距离今天的后两天日期
    getTwoDay: function (date) {
      var result = new Date((new Date(date)).getTime() + 2 * 24 * 60 * 60 * 1000);
      return result.getFullYear() + "-" + (result.getMonth() + 1) + "-" + result.getDate();
    },
    //计算距离今天的后六天日期
    getSixDay: function (date) {
      var result = new Date((new Date(date)).getTime() + 6 * 24 * 60 * 60 * 1000);
      return result.getFullYear() + "-" + (result.getMonth() + 1) + "-" + result.getDate();
    },
    //比较两日期的大小
    dateCompare: function (date1, date2) {

      var str1 = [];
      var str2 = [];
      str1 = date1.split('-');
      str2 = date2.split('-');
      if (parseInt(str1[0]) == parseInt(str2[0]) && parseInt(str1[1]) == parseInt(str2[1]) && parseInt(str1[2]) == parseInt(str2[2])) {
        return 3;
      } else {
        if (parseInt(str1[0]) > parseInt(str2[0])) {
          return 1;
        } else if (parseInt(str1[0]) < parseInt(str2[0])) {
          return 0;
        } else {
        }
        if (parseInt(str1[1]) > parseInt(str2[1])) {
          return 1;
        } else if (parseInt(str1[1]) < parseInt(str2[1])) {
          return 0;
        } else {
        }
        if (parseInt(str1[2]) > parseInt(str2[2])) {
          return 1;
        } else if (parseInt(str1[2]) < parseInt(str2[2])) {
          return 0;
        } else {
        }
        return 0;
      }

    },
    showStartEnvfun: function (dateNum, outfocus) {
      if (!outfocus) {
        if (this.startEnv == this.year + '-' + this.month + '-' + dateNum) {
          return true;
        } else {
          return false;
        }
      }
    },
    showEndEnvfun: function (dateNum, outfocus) {
      if (!outfocus) {
        if (this.endEnv == this.year + '-' + this.month + '-' + dateNum) {
          return true;
        } else {
          return false;
        }
      }
    },
    showBlack: function (dateNum, outfocus) {
      if (!outfocus) {
        if (this.dateCompare(this.startEnv, this.year + '-' + this.month + '-' + dateNum) == 0 &&
          this.dateCompare(this.year + '-' + this.month + '-' + dateNum, this.endEnv) == 0) {
          return true;
        } else {
          return false;
        }
      }
    },
    showHalffun: function (dateNum, outfocus) {
      if (!outfocus) {
        if (this.startEnv == this.year + '-' + this.month + '-' + dateNum && this.endEnv == this.year + '-' + this.month + '-' + dateNum) {
          return true;
        } else {
          return false;
        }
      }
    },
    confimDate: function () {
      // sessionStorage.workStartDate = this.startEnv;
      // sessionStorage.workEndDate = this.endEnv;
      alert('起始日期：'+this.startEnv+'---'+this.endEnv);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

@function px2rem($px, $base-font-size: 75px) {
  @return ($px / $base-font-size) * 1rem;
}

.picker-box {
  box-sizing: border-box;
  background: #fff;
  width: px2rem(750px);
  height: px2rem(920px);
  padding: px2rem(20px);
  // margin: 0 auto;
  border-radius: px2rem(5px);
  .picker-header {
    text-align: center;
    position: relative;
    margin: px2rem(20px) 0;
    .picker-year,
    .picker-month {
      margin-right: px2rem(30px);
      font-size: px2rem(30px);
      font-weight: 600;
      display: inline-block;
      color: #067ef9;
    }
    .picker-prev,
    .picker-next {
      position: absolute;
      padding: px2rem(20px) px2rem(40px);
      font-weight: 800;
      cursor: pointer;
    }
    .picker-prev {
      left: px2rem(20px);
      top: px2rem(-20px);
    }
    .picker-next {
      right: px2rem(20px);
      top: px2rem(-20px);
    }
  }
  .picker-weekday {
    width: px2rem(100px);
    float: left;
    text-align: center;
    font-size: px2rem(21px);
    padding-top: px2rem(20px);
    padding-bottom: px2rem(20px);
    color: #999999;
    font-weight: 500;
  }
  .picker-day {
    position: relative;
    margin: px2rem(5px) 0;
    font-weight: 200;
    font-size: px2rem(36px);
    font-weight: 600;
    text-align: center;
    float: left;
    width: px2rem(100px);
    height: px2rem(80px);
    line-height: px2rem(80px);
    cursor: pointer;
  }

  .picker-day.today::before {
    content: " ";
    position: absolute;
    top: 0;
    right: 0;
    width: 0;
    height: 0;
    border-top: 8px solid #1486f5;
    border-left: 8px solid transparent;
  }
  .picker-day.outfocus {
    color: #e3e3e3;
  }
  .picker-day.start {
    background: #5ec443;
  }
  .picker-day.end {
    background: #d44040;
  }
  .picker-day.black {
    background: #e2e2e2;
  }
  .picker-day.half {
    background: url('../assets/images/same-day.png') no-repeat;
    background-size: cover;
  }
  .picker-footer {
    text-align: center;
    font-size: px2rem(30px);
    div {
      width: 25%;
      height: px2rem(100px);
      line-height: px2rem(100px);
      display: inline-block;
    }
    .picker-button-today span {
      width: 0;
      display: inline-block;
      margin-right: px2rem(16px);
      border-top: 8px solid #0059bc;
      border-left: 8px solid transparent;
    }
    .picker-button-start span {
      width: px2rem(40px);
      height: px2rem(40px);
      background: #5ec443;
      display: inline-block;
      margin-right: px2rem(16px);
      vertical-align: middle;
      margin-bottom: px2rem(10px);
    }
    .picker-button-end span {
      width: px2rem(40px);
      height: px2rem(40px);
      background: #d44040;
      display: inline-block;
      margin-right: px2rem(16px);
      vertical-align: middle;
      margin-bottom: px2rem(10px);
    }
  }
  .confim {
    width: 100%;
    height: px2rem(100px);
    line-height: px2rem(100px);
    text-align: center;
    font-size: px2rem(36px);
    color: #067ef9;
    border-top: 1px solid #eee;
    cursor: pointer;
  }
}
</style>
