<template>
  <div v-bind:class="[{ blackBody: blackTheme }, { lightBody: !blackTheme }]" id="app">
    <h1 class="header">Канбан</h1>
    <div class="task-adder">
      <input
          class="task-adder__field"
          id="card-card_description-input"
          type="text"
          size="40"
          placeholder="Описание"
      />
      <button class="task_adder__add-button" @click="createCard()">&#128929;</button>
    </div>
    <div class="theme-changer">
      <input class="theme-changer__check" type="checkbox" v-model="blackTheme">
      <span class="theme-changer__indicator" v-if="blackTheme">Выключить темную тему</span>
      <span class="theme-changer__indicator" v-else>Включить темную тему</span>
    </div>
    <div class="content">
      <div class="columns-holder">
        <div
          v-for="column in columns"
          :key="column.title"
          class="columns__column"
        >
          <p class="columns__column__title">
            {{ column.title + " (" + column.tasks.length + ")" }}
          </p>
          <draggable
              class="columns__column__element"
              :list="column.tasks"
              :animation="200"
              group="tasks"
              @end="meme"
          >
            <task-card
                class="card"
                v-for="task in column.tasks"
                :key="task.id"
                :task="task"
                :columns="columns"
                @update="meme"
            ></task-card>
          </draggable>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";
import TaskCard from "./components/TaskCard.vue";
export default {
  name: "App",
  components: {
    TaskCard,
    draggable,
  },
  methods: {
    createCard() {
      this.cardId++;
      var card_description = document.getElementById(
        "card-card_description-input"
      ).value;
      if (card_description === "") {
        card_description = "Без описания";
      }
      this.columns[0].tasks.push({
        id: this.cardId,
        title: "Задача №" + this.cardId,
        description: card_description,
        startDate: null,
        endDate: null,
        reliable: null,
      });
    },
    meme() {
      this.columns[1].tasks.forEach((element) => {
        if (element.startDate == null) {
          element.startDate = this.parseDate(new Date());
        }
      });
      this.columns[1].tasks.forEach((element) => {
        if (element.reliable == null) {
          element.reliable = "Не указан";
        }
      });
      this.columns[2].tasks.forEach((element) => {
        if (element.endDate == null){
          element.endDate = this.parseDate(new Date());
        }
      });
    },
    parseDate(date){
      if (date == null){
        return null;
      }
      var dd = date.getDate();
      var mm = date.getMonth() + 1;
      var yyyy = date.getFullYear();
      var minutes = date.getMinutes();
      var hour = date.getHours();
      if(dd < 10){
        dd='0' + dd
      }
      if(mm < 10){
        mm='0' + mm
      }
      if(hour < 10){
        hour='0' + hour
      }
      if(minutes < 10){
        minutes='0' + minutes
      }
      date = yyyy + '-' + mm + '-' + dd + 'T' + hour + ':' + minutes;
      return date
    },
  },
  watch: {
    // eslint-disable-next-line no-unused-vars
    columns(newV, oldV){
      this.meme;
    }
  },
  data() {
    return {
      cardId: 0,
      blackTheme: false,
      columns: [
        {
          title: "План",
          tasks: [],
        },
        {
          title: "В работе",
          tasks: [],
        },
        {
          title: "Готово",
          tasks: [],
        },
      ],
    };
  },
};
</script>

<style scoped>
.columns__column {
  min-width: 200px;
  width: 25vh;
}
.columns-holder {
  display: flex;
  justify-content: space-around;
  align-content: center;
  min-height: 400px;
}
.blackBody, lightBody{
  margin: 0;
  height: 100vh;
}
.blackBody{
  background-color: black;
}
.blackBody *{
  background-color: black;
  color: whitesmoke;
  font-size: 18px;
  font-family: Verdana;
}
.lightBody *{
  background-color: white;
  color: black;
  font-size: 18px;
  font-family: Verdana;
}
.columns__column{
  border: deepskyblue 3px solid;
  border-radius: 15px;
}
.header{
  margin: 0;
  font-size: 40px;
}
.columns__column__title{
  border-bottom: 1px deepskyblue solid;
  padding-bottom: 1px;
}
.theme-changer{
  margin-bottom: 3%;
}
</style>
