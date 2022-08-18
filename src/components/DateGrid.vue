<template>
    <div :class="['grid-day', {'gary': !props.day.isNowMonth, 'red': props.day.isToday, 'holiday': props.day.isHoliday}]" @click=" $emit('show-todo-list', props.day.date) ">
      <div :class="['day', {'day_today':props.day.isToday, 'day_select':props.day.isSelectDay}]"> {{ dayOfdate }} </div>
      <div class="previewTodo"> 
        <ul>
          <template v-for=" (schedule, index) in props.day.schedule ">
            <li v-if="index < 2"> {{ schedule.content }}</li>
          </template>
          <li v-if="props.day.schedule.length>2">還有其他{{ props.day.schedule.length-2 }}項</li>
        </ul> 
      </div>
    </div>
</template>

<script>
import { computed } from "vue";

export default {
  name: 'DateGrid',
  props: {
    day: Object
  },
  setup(props, context) {
      const dayOfdate = computed(() => {
        let day = props.day.date.split('-')[2];
        if (day<10) {
          day = day.substr(1,1);
        }
        return day;
      });

      return {
        props,
        dayOfdate
      };
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
ul {
  color: black;
  font-size: 5px;
  margin: 0px;
  list-style-type: none;
  padding-left: 15px;
}

li {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  width: 90%;
}

.grid-day {
  cursor: pointer;
  height: 13vh;
  width: calc(95% / 7);
  background: #eaf5ff;
  display: flex;
  flex-direction: column;
}

.gary { 
 color: #b7b1ad;
}

.day_today {
  color: aliceblue;
  background: #4486ff;
  border-radius: 15px;
}

.day_select{
  color: #000;
  background: #6db6ff;
  border-radius: 15px;
}

.holiday {
  background: #c4e0ff;
}

.day{
  height: 1.5em;
  width: 20%;
  font-weight: bolder;
  text-align: center;
  line-height: 1.5em;
  align-self: end;
}

.previewTodo{
  height: calc(100% - 1.5em);
}
</style>
