<template>
    <div class="content">
        <!-- agoDayHide='1517483961000' -->
        <!-- isHideOtherday=false -->
        <Calendar ref="Calendar" v-on:choseDay="clickDay" v-on:isToday="clickToday" v-on:changeMonth="changeDate" :markArray=[20,21]></Calendar>
        <br/>
        <h1 @click="demo">标记了20号21号</h1>
    </div>
</template>

<script>
    export default {
        data() {
            return {}
        },
        methods: {
            clickDay(data) {
                console.log(data) //选中某天
                this.$toast('选中了' + data)
            },
            clickToday(data) {
                console.log(data) //跳到了本月
            },
            changeDate(data) {
                this.$toast('切换到的月份为' + data)
                console.log(data) //左右点击切换月份
            },
            demo() {
                //this.$refs.Calendar.ChoseMonth('2017-01');
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
    h1 {
        text-align: center;
    }
</style>