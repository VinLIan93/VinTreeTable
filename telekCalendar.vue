<template>
    <div class="telekCalendar">
        <!-- 星期 -->
        <ul class="weekdays" @click="Assembly(month)">
            <li style="color:red">星期日</li>
            <li style="color:#fff">星期一</li>
            <li style="color:#fff">星期二</li>
            <li style="color:#fff">星期三</li>
            <li style="color:#fff">星期四</li>
            <li style="color:#fff">星期五</li>
            <li style="color:red">星期六</li>
        </ul>
        <!-- 日期 -->
        <ul class="days">
            <li v-for="day in days" :class="day.class" @click="emitEditEvent(day)">
                <el-popover placement="bottom" trigger="hover" :open-delay="1000" width="400">
                    <el-row style="margin-bottom: 20px">
                      <el-col :span="24" style="font-size: 16px">{{day.content}}</el-col>
                    </el-row>
                    <el-row>
                      <el-col :span="24" style="font-size: 16px">生产计划描述：{{day.desc !== '' &&  day.desc !== undefined ? day.desc : '无'}}</el-col>
                    </el-row>

                    <div style="height:100%" slot="reference">
                        <div style="text-align: right">
                            <span class="dateNum">{{day.time}}</span>
                        </div>
                        <p>
                            {{day.content | contentFilter}}
                        </p>
                        </div>
                </el-popover>

                    
            </li>
        </ul>
        
    </div>
</template>

<script>
import { ajaxRequest, showUserOwnBtn, swalWarningInfo, swalSuccessInfo, getMonthDays } from '@/assets/js/util.js'

export default {
    name: 'telekCalendar',
    //注册组件
    components: {},
    //属性传值
    props: { 
        datedata: {
            default: {

            }
        },
        month: {
            type: String,
            default: '2018-7'
        }
    },
    //数据
    data() {
		return {
            days:[]
        }
	},
    //计算属性
    computed: {},
    //方法
    methods: {
        emitEditEvent(item) {
            this.$emit('editEvent',item);
        },
        /**
         * 根据输入的年月，生成日历结构数据。
         * @param date: 'yyyy-MM'
         */
        Assembly(date) {
            const self = this;
            let monthDate = new Date(date);
            let monthDays = getMonthDays(date);
            monthDate.setDate(1);
            let firstDay = monthDate.getDay();
            monthDate.setDate(monthDays);
            let lastDay = monthDate.getDay();
            let daysArr = [];
            for(let i = 1; i <= monthDays; i++) {
                let key = i<10?'0'+i:''+i;
                let dayData = self.datedata.currentData[key];
                let Obj = {
                    time: i,
                    desc: (dayData && dayData.description) ? dayData.description : '',
                    content: (dayData && dayData.value) ? `预计电量偏差：${dayData.value} kW·h` : '预计电量偏差：0',
                    class: 'current',
                    date: `${date}-${i}`
                }
                daysArr.push(Obj)
            }
            monthDate.setDate(0)
            let preDays = getMonthDays(monthDate);
            let preLen = preDays - firstDay;
            for(let i = preDays; i > preLen; i--) {
                let key = i<10?'0'+i:''+i;
                let dayData = self.datedata.previousData[key];
                let Obj = {
                    time: i,
                    desc: (dayData && dayData.description) ? dayData.description : '',
                    content: (dayData && dayData.value) ? `预计电量偏差：${dayData.value} kW·h` : '预计电量偏差：0',
                    date: `${monthDate.getFullYear()}-${monthDate.getMonth() + 1}-${i}`
                }
                daysArr.unshift(Obj);
            }
            monthDate.setDate(63);
            for(let i = 1, len = 7 - lastDay; i < len; i++) {
                let key = i<10?'0'+i:''+i;
                let dayData = self.datedata.nextData[key];
                let Obj = {
                    time: i,
                    desc: (dayData && dayData.description) ? dayData.description : '',
                    content: (dayData && dayData.value) ? `预计电量偏差：${dayData.value} kW·h` : '预计电量偏差：0',
                    date: `${monthDate.getFullYear()}-${monthDate.getMonth() + 1}-${i}`
                }
                daysArr.push(Obj);
            }
            return daysArr;
        },

        updateCalendarData() {
            this.days = this.Assembly(this.month);
        }
    },
    //过滤器
    filters: {
        contentFilter(value) {
            return value.replace('预计电量偏差：', '');
        }
    },
    //生命周期钩子——组件实例刚被创建，组件属性计算之前
    beforeCreate() {},
    //生命周期钩子——组件实例创建完成，属性已绑定，但DOM未生成
    created() {},
    //生命周期钩子——完成了 el 和 data 初始化，模拟编译/挂载之前
    beforeMount() {},
    //生命周期钩子——完成了 el 和 data 初始化，模拟编译/挂载完成
    mounted() {},
    //生命周期钩子——组件更新之前
    beforeUpdate() {},
    //生命周期钩子——组件更新之后
    updated() {},
    //生命周期钩子——组件销毁前调用
    beforeDestroy() {},
    //生命周期钩子——组件销毁后调用
    destroyed() {},
    //侦听器
    watch: {}
    
}
</script>

<style>
    .telekCalendar{
        width: 100%;
        height: 100%;
    }
    .telekCalendar .weekdays{
        width: 100%;
        height: 40px;
        line-height: 38px;
        display: flex;
        /* border: 2px solid #e4e7ed; */
        box-sizing: border-box;
        border-radius: 3px 3px 0 0;
        background: #2d3339;
    }
    .telekCalendar .weekdays li{
        list-style: none;
        text-align: center;
        width: 14.2%;
        flex-grow: 1;
        flex-shrink: 1;
        border-right: 1px solid #c3c6cd;
        box-sizing: border-box; 
        font-size: 16px;
    }
    .telekCalendar .weekdays li:last-child{
        border-right: 0;
    }
    .telekCalendar .days{
        width: 100%;
        height: calc(100% - 40px);
        display: flex;
        flex-wrap: wrap;
        border: 1px solid #c3c6cd;
        border-bottom: 0;
        box-sizing: border-box;
    }
    .telekCalendar .days li{
        width: 14.2%;
        flex-grow: 1;
        flex-shrink: 1;        
        list-style: none;
        border-right: 1px solid #c3c6cd;
        border-bottom: 1px solid #c3c6cd;
        box-sizing: border-box;
        background: #e8e7e7;
        display: flex;
        flex-flow: column;
        overflow: hidden;
        min-height: 16.7%;
        padding: 10px 10px 0;
    }
    .telekCalendar .days li>span{
        display: block;
        height: 100%;
    }

    .telekCalendar .days li.current{
        background: #fff;
    }
    .telekCalendar .days li:nth-child(7n){
        border-right: 0;
    }
    .telekCalendar .days li .dateNum{
        font-size: 14px;
        font-weight: bold;
        flex-shrink: 0;
        flex-grow: 0;
    }
    .telekCalendar .days li p{
        height: calc(100% - 25px);
        display: flex;
        align-items: center;
        justify-content: left;
        font-size: 14px;
        /* flex-shrink: 1;
        flex-grow: 1;
        overflow: hidden;
        display: -webkit-box;
        -webkit-box-orient: vertical;
        -webkit-line-clamp: 2;   */
        overflow: hidden;
    }
    /* @media screen and (max-height: 560px){
        .telekCalendar .days li p{
            max-height: 0;
            -webkit-line-clamp: 1;
        }
    }
    @media screen and (max-height: 650px) and (min-height: 560px){
        .telekCalendar .days li p{
            max-height: 18px;
            -webkit-line-clamp: 1;
        }
    }
    @media screen and (max-height: 750px) and (min-height: 650px){
        .telekCalendar .days li p{
            max-height: 35px;
            -webkit-line-clamp: 2;
        }
    }
    @media screen and (max-height: 850px) and (min-height: 750px){
        .telekCalendar .days li p{
            max-height: 50px;
            -webkit-line-clamp: 3;
        }
    }
    @media screen and (min-height: 850px){
        .telekCalendar .days li p{
            max-height: 65px;
            -webkit-line-clamp: 4;
        }
    } */


</style>


