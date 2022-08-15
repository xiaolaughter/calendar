<template>
  <!-- if click day, show to do screen -->
  <ScheduleScreen v-if="isShowToDoList" :date="selectDate" :schedule="selectDaySchedule" @close="isShowToDoList=false" @add-list-item="addListItem" @del-list-item="delListItem"></ScheduleScreen>
  
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
        <div v-text="weekName" :class="['week', { 'weeknormalday': isWeekNormalDay(weekName), 'weekholiday': !isWeekNormalDay(weekName) }]" v-for="weekName in weekNames" :key="weekName"></div>
        <!-- day component -->
        <DateGrid :day="dayObj" v-for="dayObj in canlenderArr" :key="dayObj" @show-todo-list="showListContent"></DateGrid>
      </div>
    </div>
    


  
</template>

<script>
import DateGrid from './components/DateGrid.vue'
import TodoSideBar from './components/TodoSideBar.vue'
import { ref, computed, onMounted } from "vue";

export default {
  name: 'App',
  components: {
    DateGrid,
    TodoSideBar
  },
  setup() {
      const defaultSchedule = ref([]);
      /*
      defaultSchedule formate
      {
        id: date.getTime(),
        date: 'yyyy-m-d',
        content: string,
        state: bool,
        edit: bool
      }
      */
      const date = ref(new Date());
      const selectDate = ref( date.value.toLocaleDateString().replaceAll('/','-') );

      onMounted(() => {
        defaultSchedule.value.push({
          id: 1658107621839,
          date: '2022-8-10',
          content: 'edit to do content',
          state: false,
          edit: false
        });
        defaultSchedule.value.push({
          id: 1658107621840,
          date: '2022-8-10',
          content: 'edit to do content',
          state: false,
          edit: false
        });
        defaultSchedule.value.push({
          id: 1658107621841,
          date: '2022-8-9',
          content: 'edit to do content',
          state: false,
          edit: false
        });
      });

      const nextMonth = function() {
        var dateNextMonth = new Date(date.value.getTime());
        dateNextMonth.setDate(this.dayCountOfMonth);
        dateNextMonth = new Date(dateNextMonth.getTime() + (60 * 60 * 24 * 1000));
        date.value = dateNextMonth;
      }

      const preMonth = function() {
        var preMonthDate = new Date(date.value.getTime());
        preMonthDate.setDate(1);
        preMonthDate = new Date(preMonthDate.getTime() - (60 * 60 * 24 * 1000));
        date.value = preMonthDate;
      }

      const addListItem = function(date) {
        defaultSchedule.value.push({
          id: new Date().getTime(),
          date: selectDate.value,
          content: '',
          state: false,
          edit: true
        });
        console.log(defaultSchedule.value);
      }

      const delListItem = function(id) {
        defaultSchedule.value = defaultSchedule.value.filter((item) => {
          return item.id != id;
        })
      }

      const showListContent = function(dateStr) {
        selectDate.value = dateStr;
      };

      const isWeekNormalDay = function(weekName){
        return weekName != 'SAT' && weekName != 'SUN';
      };

      const selectDaySchedule = computed(() => {
        return defaultSchedule.value.filter((v) => {
          return v.date == selectDate.value;
        });
      });

      const dateTitle_full = computed(() => {
        return date.value.getFullYear() + '-' + date.value.getMonth() + '-' + date.value.getDay();
      });

      const dateTitle_month = computed(() => {
        return date.value.getFullYear() + '-' + (date.value.getMonth()+1);
      });
      

      const dayCountOfMonth = computed(() => {
        var allDayDate = new Date(date.value.getTime());
        allDayDate.setDate(28);
        allDayDate = new Date(allDayDate.getTime() + 60 * 60 * 24 * 4 * 1000);
        allDayDate.setDate(1);
        allDayDate = new Date(allDayDate.getTime() - 60 * 60 * 24 * 1000);
        return allDayDate.getDate();
      });

      const canlenderArr = computed(() => {
        console.log("canlenderArr");
        var nowCanlenderContent = [],
          today = new Date(),
          startDate = new Date(date.value.getTime()),
          nowMonth = startDate.getMonth();

        startDate.setDate(1);
        startDate = new Date(startDate.getTime() - (startDate.getDay() * 24 * 60 * 60 * 1000));
        let dateStr = startDate.getFullYear() + '-' + (startDate.getMonth() + 1) + '-' + startDate.getDate();
        for (var i = 0; i < 42; i++) {
          let dateInFor = startDate.toLocaleDateString().replaceAll('/', '-');
          let isToday = today.getMonth() == startDate.getMonth() && today.getDate() == startDate.getDate();
          nowCanlenderContent.push({
            date: dateInFor,
            isNowMonth: nowMonth != startDate.getMonth(),
            isToday: isToday,
            isSelectDay: (dateInFor == selectDate.value) && !isToday,
            isHoliday: i % 7 == 0 || i % 7 == 6,
            schedule: defaultSchedule.value.filter((v) => { return v.date == dateInFor })
          });
          startDate = new Date(startDate.getTime() + (24 * 60 * 60 * 1000));
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
