<template>
  <div class="task">
    <div class="title-task">
      <p class="title-task__text">{{task.title}}</p>
    </div>
    <div class="task-data">
      <div class="task-data__element"><p class="task-data__element__text">Описание: </p>{{task.description}}</div>
      <div class="task-data__element" v-if="task.startDate != null"><p class="task-data__element__text">Дата и время начала: </p> {{new Date(task.startDate).toLocaleString()}}</div>
      <div class="task-data__element" v-if="task.startDate != null && task.endDate != null"><p class="task-data__element__text">Ушло времени: </p>{{getTimeWasted}}</div>
      <div class="task-data__element" v-if="task.reliable != null"><p class="task-data__element__text">Ответственный: </p>{{task.reliable}}</div>
      <button class="just-button" @click="log"><span v-if="this.findIndex() === 2">Удалить</span><span v-else>Выполнено</span></button>
      <button class="just-button" @click="openForm">Редактировать</button>
    </div>
    <vue-modaltor :visible="open" @hideModal="hideModal" class="card-all-data" drag>
      <template #body class="card-all-data__body">
        <form v-if="open" class="card-all-data__form">
          <div class="card-all-data__form__input-title">Описание</div>
          <input
              class="card-all-data__form__input-field"
              v-model="description_input"
          />
          <div class="card-all-data__form__input-title">Статус</div>
          <select class="card-all-data__form__input-field" v-model="status_input">
            <option value="0">План</option>
            <option value="1">В работе</option>
            <option value="2">Готово</option>
          </select>
          <div class="card-all-data__form__input-title">Ответственный</div>
          <input
              class="card-all-data__form__input-field"
              v-model="reliable_input"
          />
          <div class="card-all-data__form__input-title">Дата и время начала</div>
          <input
              class="card-all-data__form__input-field"
              type="datetime-local"
              v-model="start_date_input"
          />
          <div class="card-all-data__form__input-title">Дата и время завершения</div>
          <input
              class="card-all-data__form__input-field"
              type="datetime-local"
              v-model="end_date_input"
          />
          <button class="just-button" type='button' @click="saveChanges">Сохранить</button>
        </form>
      </template>
    </vue-modaltor>
  </div>
</template>
<script>
export default {
  name: "TaskCard",
  methods: {
    log(){
      var el = this.task;
      if(this.findIndex()+1 < 3){
        this.columns[this.findIndex()+1].tasks.push(el);
      }
      var indexOfEl = this.columns[this.findIndex()].tasks.indexOf(el);
      this.columns[this.findIndex()].tasks.splice(indexOfEl, 1);
      this.$emit('update', null);
    },
    moveTask(){
      var el = this.task;
      var oldIndex = this.findIndex();
      var indexOfEl = this.columns[oldIndex].tasks.indexOf(el);
      this.columns[this.status_input].tasks.push(el);
      this.columns[oldIndex].tasks.splice(indexOfEl, 1);
      this.$emit('update', null);
    },
    findIndex(){
      let index=0;
      for (let i=0; i < 3; i++){
        if (this.columns[i].tasks.some(this.isThisElement)){
          return index;
        }
        index++;
      }
      return null;
    },
    isThisElement(element) {
      return element.id === this.task.id;
    },
    hideModal() {
      this.open = false;
    },
    openForm(){
      this.description_input = this.task.description;
      this.status_input = this.findIndex();
      this.reliable_input = this.task.reliable;
      this.start_date_input = this.task.startDate;
      this.end_date_input = this.task.endDate;
      this.open = true;
    },
    saveChanges(){
      this.task.description = this.description_input;
      this.task.reliable = this.reliable_input;
      this.task.startDate = this.start_date_input;
      if (this.end_date_input != this.task.endDate){
        this.task.endDate = this.end_date_input;
      }
      this.moveTask();
      this.hideModal();
    }
  },
  props: {
    columns: {
      type: Array
    },
    task: {
      type: Object,
      default: () => ({})
    }
  },
  data() {
    return {
      open: false,
      description_input: "",
      status_input : 0,
      reliable_input: "",
      start_date_input: null,
      end_date_input: null,
    };
  },
  computed:{
    getTimeWasted(){
      var mills;
      mills = new Date(this.task.endDate) - new Date(this.task.startDate);
      var sec = Math.floor(mills / 1000);
      var min = Math.floor(sec / 60);
      var hours = Math.floor(min / 60);
      var days = Math.floor(hours / 24);
      min = min - hours * 60;
      hours = hours - days * 24;
      return `Дни: ${days}; часы: ${hours}; минуты: ${min};`;
    }
  }
};
</script>
<style>
.modaltor__overlay{
  background-color: inherit !important;
}
.modaltor__panel{
  background-color: deepskyblue !important;
  border-radius: 10px !important;
  box-shadow: 0 0 10px !important;
}
.just-button{
  background-color: deepskyblue;
  border-radius: 10px;
  border: 1px whitesmoke solid;
  color: inherit;
  font-size: 16px;
}
.task-data__element__text, .title-task__text {
  font-weight: bold;
}
.task{
  border-bottom: 1px deepskyblue solid;
  margin-bottom: 10px;
  padding-bottom: 10px;
}
.task-data{
  text-align: center;
}
</style>
