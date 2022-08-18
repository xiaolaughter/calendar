<template>
    <div id="contianerBar">
      <div id="day"> {{ day_title }} </div>
      <div id="month_year"> {{ month_year_title }} </div>
      <div id="todoList" :class="{'todolist-none':isNoneSchedule}" @dblclick=" $emit('add-list-item') ">
        <div v-if="isNoneSchedule">double click here to add new ToDoList</div>
        <template v-for="listItem in props.schedule" :key="listItem">
            <div class="listItem">
              <input type="checkbox" class="listItem-checkbox" v-model="listItem.state">
              <div class="listItem-text">
                <input placeholder="input ToDoList " class="listItem-edit" v-focus v-if="listItem.edit" type="text" v-model="listItem.content" @blur="verifyToDoListContent(listItem)"
                  @keyup.enter="verifyToDoListContent(listItem)">
                <div v-else class="" @click="listItem.edit=true">{{listItem.content}}</div>
              </div>
              <div class="listItem-del" @click=" $emit('del-list-item', listItem.id)"></div>
            </div>
          </template>
      </div>
    </div>
</template>

<script>
import { computed } from "vue";
import { DateTime } from "luxon";

const focus = {
  mounted: (el) => el.focus()
}

export default {
  name: 'TodoSideBar',
  props: {
    date: String,
    schedule: Array
  },
  directives: {
    focus
  },
  setup(props, context) {
    const month_year_title = computed(() => {
      if (props.date == '') { return ''; }
      const monthNameArr = ['January', 'February', 'March', 'April', 'May', 'June',
                      'July', 'August', 'September', 'October', 'November', 'December'];
      let monthName = monthNameArr[props.date.split('-')[1] - 1];
      return monthName + ' ' + props.date.split('-')[0];
    });

    const day_title = computed(() => {
      if (props.date == '') { return ''; }
      return props.date.split('-')[2];
    });

    const isNoneSchedule = computed(()=>{
      return props.schedule.length == 0;
    });

    const verifyToDoListContent = listItem => {
      if(listItem.content.trim() == ''){
        context.emit('del-list-item', listItem.id);
      }else{
        listItem.edit = false;
      }
    };

    return {
      props,
      month_year_title,
      day_title,
      isNoneSchedule,
      verifyToDoListContent
    };
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #contianerBar{
    width: 35vw;
    height: 100vh;
    background: aliceblue;
  }

  #day{
    width: 100%;
    height: calc(14vh + 20px);
    font-size: 100px;
    text-align: center;
    line-height: calc(14vh + 20px);
    color: #3e85fa;
  }

  #month_year{
    width: 100%;
    height: 5vh;
    background: #d5f2ff;
    text-align: center;
    font-size: 23px;
    color: #2374ba;
    line-height: 5vh;
  }

  #todoList{
    width: 100%;
    height: calc(81vh - 20px);
  }

  .todolist-none{
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
  }

  .listItem {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 10px;
  }

  .listItem-checkbox{
    width: 1.5em;
    height: 1.5em;
  }

  .listItem-text{
    margin-left: 5px;
    margin-right: 10px;
    flex-grow: 1;
    max-width: 80%;
    text-overflow: ellipsis;
  }

  .listItem-edit{
    
  }

  .listItem-del{
    height: 1.5em;
    width: 1.5em;
    text-align: center;
  }

  .listItem-del::after{
    width: 100%;
    height: 100%;
    display: inline-block;
    content: "\00d7";
  }

  .listItem-del:hover::after{
    width: 100%;
    height: 100%;
    background: gainsboro;
    border-radius: 20px;
    display: inline-block;
    content: "\00d7";
  }
</style>
