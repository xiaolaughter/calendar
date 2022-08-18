<template>
    <!-- left area -->  
    <TodoSideBar :date="selectDate" :schedule="selectDaySchedule" @add-list-item="addListItem" @del-list-item="delListItem"></TodoSideBar>
    <div>
      <div class='calendar'>
        <!-- month change -->
        <div class='month_switch'>
          <input type="button" name="" value="＜" @click="preMonth()">
          <div class="month_switch_text"> {{ dateTitle_month }} </div>
          <input type="button" name="" value="＞" @click="nextMonth()"> 
        </div>
         <!-- week header -->
        <div v-for="weekName in weekNames" :key="weekName" :class="['week', { 'weeknormalday': isWeekNormalDay(weekName), 'weekholiday': !isWeekNormalDay(weekName) }]"> {{ weekName }} </div>
        <!-- day component -->
        <DateGrid v-for="dayObj in canlenderArr" :day="dayObj" :key="dayObj" @show-todo-list="showListContent"></DateGrid>
      </div>
    </div>
    


  
</template>

<script>
import FackData from './assets/FackData.js'
import DateGrid from './components/DateGrid.vue'
import TodoSideBar from './components/TodoSideBar.vue'
import { ref, computed, onMounted } from "vue";
import { DateTime } from "luxon";

export default {
  name: 'App',
  components: {
    DateGrid,
    TodoSideBar
  },
  setup() {
      const defaultSchedule = ref([]);
      const date = ref(DateTime.now());
      const selectDate = ref( date.value.toFormat('yyyy-MM-dd') );

      onMounted(() => {
        defaultSchedule.value = FackData.fackSchedule;
      });

      const nextMonth = function() {
        date.value = date.value.plus({month: 1})
      }

      const preMonth = function() {
        date.value = date.value.plus({month: -1})
      }

      const addListItem = date => {
        defaultSchedule.value.push({
          id: new Date().getTime(),
          date: selectDate.value,
          content: '',
          state: false,
          edit: true
        });
      }

      const delListItem = id => {
        defaultSchedule.value = defaultSchedule.value.filter( item => item.id != id );
      }

      const showListContent = dateStr => {
        selectDate.value = dateStr;
      };

      const isWeekNormalDay = weekName => weekName != 'SAT' && weekName != 'SUN';
      

      const selectDaySchedule = computed(() => {
        return defaultSchedule.value.filter( schedule => schedule.date == selectDate.value );
      });

      const dateTitle_full = computed(() => {
        return date.value.toFormat('yyyy-MM-dd') ;
      });

      const dateTitle_month = computed(() => {
        return date.value.toFormat('yyyy-MM') ;
      });
      

      const dayCountOfMonth = computed(() => {
        return date.value.endOf('month').toObject().day;
      });

      const canlenderArr = computed(() => {
        let nowCanlenderContent = [],
          today = DateTime.now(),
          startDate = date.value.startOf('month');
          startDate = startDate.plus({ day: -startDate.weekday });

        for (let i = 0; i < 42; i++) {
          let dateInFor = startDate.toFormat('yyyy-MM-dd');
          let isToday = today.year == startDate.year && today.month == startDate.month && today.day == startDate.day;
          nowCanlenderContent.push({
            date: dateInFor,
            isNowMonth: date.value.month == startDate.month,
            isToday: isToday,
            isSelectDay: (dateInFor == selectDate.value) && !isToday,
            isHoliday: i % 7 == 0 || i % 7 == 6,
            schedule: defaultSchedule.value.filter((schedule) => { return schedule.date == dateInFor })
          });

          startDate = startDate.plus({ day: 1 });
        }
        return nowCanlenderContent;
      });

      return {
        date,
        selectDate,
        selectDaySchedule,
        weekNames: ['SUN', 'MON', 'TUES', 'WED', 'THUR', 'FRI', 'SAT'],
        nextMonth,
        preMonth,
        dateTitle_full,
        dateTitle_month,
        dayCountOfMonth,
        isWeekNormalDay,
        canlenderArr,
        showListContent,
        addListItem,
        delListItem
      };
    }
}
</script>

<style>
  .calendar {
    width: 65vw;
    height: 100vh;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
    align-content: space-around;
  }

  .week {
    height: 5vh;
    width: calc(95% / 7);
  }

  .month_switch{
    width: 100%;
    height: 5vh;
    display: flex;
  }

  .month_switch_text{
    line-height: 5vh;
    font-size: 20px;
    font-weight: 800;
    color: #0199ed;
  }

  .month_switch input{
    margin-right: 15px;
    margin-left: 15px;
    appearance: none;
    border: 0px;
    background: none;
    font-weight: bold;
  }

  #contianerTitle {
    display: flex;
    justify-content: space-evenly;
    font-size: 25px;

    color: aliceblue;
    font-size: 51px;
    width: 100vw;
    height: 25vh;
    background: repeating-linear-gradient(135deg, #2B3284 /*0px*/, #47a5fc 100px, #4177BC 100px, #4177BC 105px, #2B3284 105px, #2B3284 130px, #3a62c3 130px, #4177BC 260px, #47a5fc 260px, #fff 265px, #4177BC 265px, #4177BC 270px, #547bd8 270px, #547bd8 330px );
  }

  .weeknormalday{
    background: #eaf5ff;
    color: #6ba8ff;
    line-height: 5vh;
    text-align: center;
  }

  .weekholiday{
    background: #c0deff;
    color: #0007ff;
    line-height: 5vh;
    text-align: center;
  }

  

  .container_week{
    width: calc(95% / 7);
    text-align: center;
    line-height: 5vh;
    font-family: cursive;
    font-weight: 900;
  }
</style>
