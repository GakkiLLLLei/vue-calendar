<style>
  * {
    margin: 0;
    padding: 0;
  }

  .wh_container {
    max-width: 410px;
    margin: auto;
  }

  li {
    list-style-type: none;
  }

  .wh_content_all {
    font-family: -apple-system, BlinkMacSystemFont, 'PingFang SC', 'Helvetica Neue', STHeiti, 'Microsoft Yahei', Tahoma,
    Simsun, sans-serif;
    background-color: #0fc37c;
    width: 100%;
    overflow: hidden;
    padding-bottom: 8px;
  }

  .wh_content {
    display: flex;
    flex-wrap: wrap;
    padding: 0 3% 0 3%;
    width: 100%;
  }

  .wh_content:first-child .wh_content_item {
    color: #ddd;
    font-size: 16px;
    height: 15%;
    line-height: 15%;
  }

  .wh_content_item {
    font-size: 15px;
    width: 13.4%;
    padding-bottom: 10.14%;
    text-align: center;
    color: #fff;
    position: relative;
  }

  .wh_content_item div {
    position: absolute;
    top: 0;
    width: 100%;
    height: 100%;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .wh_content_item li {
    width: 37px;
    height: 37px;
    border-radius: 100px;
    line-height: 37px;
  }

  .wh_content_item li:hover {
    background: #71c7a5;
    cursor: pointer
  }

  .wh_top_changge {
    display: flex;
  }

  .wh_top_changge li {
    cursor: pointer;
    display: flex;
    color: #fff;
    font-size: 18px;
    flex: 1;
    justify-content: center;
    align-items: center;
    height: 47px;
  }

  .wh_top_changge .wh_content_li {
    cursor: auto;
    flex: 2.5;
  }

  .wh_jiantou1 {
    width: 12px;
    height: 12px;
    border-top: 2px solid #ffffff;
    border-left: 2px solid #ffffff;
    transform: rotate(-45deg);
  }

  .wh_jiantou1:active,
  .wh_jiantou2:active {
    border-color: #ddd;
  }

  .wh_content_item div .wh_is_today {
    margin: auto;
    background-color: #fff;
    color: #51c597;
    border-radius: 100px;
    text-align: center;
  }

  .wh_jiantou2 {
    width: 12px;
    height: 12px;
    border-top: 2px solid #ffffff;
    border-right: 2px solid #ffffff;
    transform: rotate(45deg);
  }

  .wh_content_item div .wh_is_mark {
    margin: auto;
    border-radius: 100px;
    border: 1px solid blue;
    z-index: 2;
  }

  .wh_content_item div .wh_next_day_show {
    color: #bfbfbf;
  }
</style>
<template>
  <section class="wh_container">
    <div class="wh_content_all">
      <div class="wh_top_changge">
        <li @click="PreMonth">
          <div class="wh_jiantou1"></div>
        </li>
        <li class="wh_content_li">{{date_top}}</li>
        <li @click="NextMonth">
          <div class="wh_jiantou2"></div>
        </li>
      </div>
      <div class="wh_content">
        <div class="wh_content_item" v-for="tag in text_top">
          <div>
            {{tag}}
          </div>
        </div>
      </div>
      <div class="wh_content">
        <div class="wh_content_item" v-for="(item,index) in list" @click="clickDay(item,index)">
          <div>
            <li v-bind:class="{ wh_is_today: item.is_today,
                                        wh_is_mark:item.is_mark,
                                        wh_next_day_show:(isHideOtherday&&item.next_day_show)||item.other_month||item.agoDayHide}">
              {{item.id}}</li>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
<script>
  export default {
    data() {
      return {
        text_top: ['一', '二', '三', '四', '五', '六', '日'],
        my_data: [],
        list: [],
        date_top: ''
      };
    },
    props: {
      markArray: { default: '[]' },
      agoDayHide: { default: '0' },
      isHideOtherday: { default: false }
    },
    created() {
      this.my_data = new Date();
    },
    methods: {
      clickDay: function (item, index) {
        if (item.other_month) {
          item.other_month < 0 ? this.PreMonth() : this.NextMonth();
        } else {
          if (!(this.isHideOtherday && item.next_day_show) && !item.agoDayHide) {
            this.$emit('choseDay', item.date);
            for (var i = 0; i < this.list.length; i++) {
              if (i == index) {
                this.list[i].is_today = true;
              } else {
                this.list[i].is_today = false;
              }
            }
          }
        }
      },
      ChoseMonth: function (date) {
        this.my_data = new Date(date);
        this.$emit('changeMonth', this.dateformat(this.my_data));
        this.getlist(this.my_data);
      },
      PreMonth: function () {
        this.my_data = this.getPreMonth(this.my_data);
        this.$emit('changeMonth', this.dateformat(this.my_data));
        this.getlist(this.my_data);
      },
      NextMonth: function () {
        this.my_data = this.getNextMonth(this.my_data);
        this.$emit('changeMonth', this.dateformat(this.my_data));
        this.getlist(this.my_data);
      },
      /**
       * 获取上一个月
       */
      getPreMonth: function (date) {
        var time_array = this.dateformat(date).split('/');
        var year = time_array[0]; //获取当前日期的年份
        var month = time_array[1]; //获取当前日期的月份
        var day = time_array[2]; //获取当前日期的日
        var days = new Date(year, month, 0);
        days = days.getDate(); //获取当前日期中月的天数
        var year2 = year;
        var month2 = parseInt(month) - 1;
        if (month2 == 0) {
          year2 = parseInt(year2) - 1;
          month2 = 12;
        }
        var day2 = day;
        var days2 = new Date(year2, month2, 0);
        days2 = days2.getDate();
        if (day2 > days2) {
          day2 = days2;
        }
        if (month2 < 10) {
          month2 = '0' + month2;
        }
        var t2 = year2 + '/' + month2 + '/' + day2;
        return new Date(t2);
      },
      /**
       * 获取下一个月
       */
      getNextMonth: function (date) {
        var arr = this.dateformat(date).split('/');
        var year = arr[0]; //获取当前日期的年份
        var month = arr[1]; //获取当前日期的月份
        var day = arr[2]; //获取当前日期的日
        var days = new Date(year, month, 0);
        days = days.getDate(); //获取当前日期中的月的天数
        var year2 = year;
        var month2 = parseInt(month) + 1;
        if (month2 == 13) {
          year2 = parseInt(year2) + 1;
          month2 = 1;
        }
        var day2 = day;
        var days2 = new Date(year2, month2, 0);
        days2 = days2.getDate();
        if (day2 > days2) {
          day2 = days2;
        }
        if (month2 < 10) {
          month2 = '0' + month2;
        }

        var t2 = year2 + '/' + month2 + '/' + day2;
        return new Date(t2);
      },
      getDaysInOneMonth: function (date) {
        //通过获取下月面0号的日期可以知道这个月有多少天
        var getyear = date.getFullYear();
        var getmonth = date.getMonth() + 1;
        getmonth = parseInt(getmonth, 10);
        var d = new Date(getyear, getmonth, 0);
        return d.getDate();
      },
      getMonthweek: function (date) {
        //获取本月第一天是星期几，然后在去向前空几个
        var getyear = date.getFullYear();
        var getmonth = date.getMonth() + 1;
        var date_one = new Date(getyear + '/' + getmonth + '/1');
        return date_one.getDay() == 0 ? 6 : date_one.getDay() - 1;
      },
      getlist: function (date) {
        //渲染出来当前list
        var mygetMonth = date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1;
        this.date_top = date.getFullYear() + '年' + mygetMonth + '月';
        var array = [];
        for (var i = 0; i < this.getDaysInOneMonth(date); i++) {
          if (
            this.dateformat(new Date()) ==
            this.dateformat(new Date(date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1)))
          ) {
            array = array.concat({
              //如果当前这天是今天 is_today是true
              id: i + 1,
              date: date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1),
              is_today: true,
              is_mark: this.markArray.indexOf(i + 1) >= 0,
              agoDayHide: new Date(`${date.getFullYear()}-${mygetMonth}-${i + 1}`).getTime() < this.agoDayHide * 1,
              next_day_show:
                new Date(date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1)).getTime() -
                new Date().getTime() >
                0
            });
            this.$emit(
              'isToday',
              this.dateformat(new Date(date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1)))
            );
          } else {
            array = array.concat({
              id: i + 1,
              date: date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1),
              is_today: false,
              is_mark: this.markArray.indexOf(i + 1) >= 0,
              agoDayHide: new Date(`${date.getFullYear()}-${mygetMonth}-${i + 1}`).getTime() < this.agoDayHide * 1,
              next_day_show:
                new Date(date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1)).getTime() -
                new Date().getTime() >
                0
            });
          }
        }
        var array2 = [];
        var num = this.getDaysInOneMonth(this.getPreMonth(date)) - this.getMonthweek(date) + 1;
        for (var i = 0; i < this.getMonthweek(date); i++) {
          array2 = array2.concat({ other_month: -1, id: num + i });
        }
        array = array2.concat(array);
        var length_ = 7 - array.length % 7;
        if (length_ < 7) {
          for (let i = 0; i < length_; i++) {
            array.push({ other_month: 1, id: i + 1 });
          }
        }
        this.list = array;
      },
      dateformat: function (date) {
        return date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + date.getDate();
      }
    },
    mounted() {
      this.getlist(this.my_data);
    },
    watch: {
      markArray(val, oldVal) {
        var list = this.list;
        for (var i = 0; i < list.length; i++) {
          list[i].is_mark = false;
          if (list[i].date) {
            for (var n = 0; n < val.length; n++) {
              if (list[i].id == val[n]) {
                list[i].is_mark = true;
              }
            }
          }
        }
        this.list = list;
      }
    }
  };
</script>