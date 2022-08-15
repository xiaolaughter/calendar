<template>
    <div id="contianerToDoList" @click="close">
      <div id="contentToDoList" @click.stop="">
        <div id="title">
          <div>
            {{ dateTitle }}
          </div>
        </div>
        <div id="contianerList">
          <template v-for="listItem in props.schedule" :key="listItem">
            <div class="listItem-container">
              <input type="checkbox" v-model="listItem.state">
              <div class="listItem-text">
                <input class="listItem-input " v-focus v-if="listItem.edit" type="text" v-model="listItem.content" @blur="listItem.edit = false;"
                @keyup.enter="listItem.edit=false;">
                <div v-else class="" @click="listItem.edit=true">{{listItem.content}}</div>
              </div>
              <div class="listItem-btn-del" @click="emit('del-list-item', listItem.id)"></div>
            </div>
          </template>
        </div>
        <div id="btnAddList" @click="emit('add-list-item')">
          <div>
            ＋
          </div>
        </div>
      </div>
    </div>
</template>

<script>
import { computed } from "vue";

const focus = {
  mounted: (el) => el.focus()
}

export default {
  name: 'ScheduleScreen',
  props: {
    schedule: Array,
    date: String
  },
  directives: {
    focus
  },
  setup(props, context) {
      const close = function() {
        context.emit('close', props.schedule)
      }

      const dateTitle = computed(() => {
        let dateArr = props.date.split('-');
        return dateArr[0] + '年' + dateArr[1] + '月' + dateArr[2] + '日';
      });

      return {
        props,
        emit: context.emit,
        close,
        dateTitle
      }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#contianerToDoList {
  width: 100vw;
  height: 100vh;
  background: #00000066;
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
}

#contentToDoList {
  width: 50vw;
  height: 95vh;
  border-radius: 5px;
  background: #fff7ea;
}

#title {
  width: 100%;
  height: 10%;
  display: flex;
  justify-content: center;
  align-items: center;
  border-bottom: solid 3px #000000;
}

#title>div {
  text-align: center;
  font-weight: bold;
  font-size: 30px;
}

#contianerList {
  width: 100%;
  height: 80%;
}

#btnAddList {
  width: 100%;
  height: 10%;
  display: flex;
  justify-content: center;
  align-items: center;
  border-top: solid 3px #000000;
  box-sizing: border-box;
}

#btnAddList:hover {
  background: #59d159;
}

#btnAddList>div {
  font-size: 35px;
  font-weight: bolder;
}

.listItem-container {
  display: grid;
  height: 2em;
  grid-template-columns: repeat(20, 1fr);
}

.listItem-text {
  grid-column: 3/17;
  display: flex;
  align-items: center;
}

.listItem-input {
  width: 100%;
  height: 70%;
}

.listItem-btn-del {
  position: relative;

  width: 100%;
  grid-column: 20;
}

.listItem-btn-del:hover {
  border: solid 2px #000000;
  box-sizing: border-box;
}

.listItem-btn-del::after {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  content: "\274c";
  font-size: 20px;
  line-height: 32px;
  text-align: center;
}
</style>
