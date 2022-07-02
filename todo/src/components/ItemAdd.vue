<template>
  <div id="modal-background">
      <div class="modal fade" aria-modal="true" role="dialog" id="dlgEdit">
        <div class="modal-dialog modal-lg">
          <div class="modal-content">
            <div class="modal-header">
              <h4 class="modal-title">Задача</h4>
            </div>
            <div class="modal-body">
              <div class="control-group">
                <label for="name">Название задачи:</label>
                <input maxlength="100" v-model="newTodo.title" class="mx-input input">
              </div>
              <div class="control-group">
                <label>Описание:</label>
                <input maxlength="2048" v-model="newTodo.description" class="mx-input">
              </div>
              <div class="control-group">
                <label>Теги:</label>
                <input v-model="newTodo.tags" class="mx-input">
              </div>
              <div class="control-group">
                <label>Дедлайн:</label>
                  <DatePicker style="display: flex" v-model="newTodo.date" type="datetime" format="DD.MM.YYYY HH:mm"></DatePicker>
              </div>
              <div class="control-group" v-if="newTodo.id != ''">
                <label>Статус:</label>
                <select v-model="newTodo.status" class="mx-input">
                  <option value="В работе">В работе</option>
                  <option value="Выполнено">Выполнено</option>
                  <option value="Просрочено">Просрочено</option>
                </select>
              </div>            
            </div>
            <div class="modal-footer">
              <button class="button button-green" v-on:click="save">Сохранить</button>
              <button class="button button-red pad-left" v-on:click="close">Отмена</button>
            </div>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
import DatePicker from 'vue2-datepicker';
import moment from 'moment';
import { v4 as uuidv4 } from 'uuid';
import 'vue2-datepicker/index.css';
export default {
  name: 'ItemAdd',
  components: {
    DatePicker
  },
  data() {
    return {
      dlgEdit: '',
      newTodo: {
        id: '',
        title: '',
        date: 'Нет',
        status: 'В работе',
        description: '',
        tags: '',
      },
    }
  },
  methods: {
    preparing(data) {
      this.newTodo.id = data.id;
      this.newTodo.title = data.title;
      this.newTodo.date = data.date != 'Нет' ? data.date : 'Нет';
      this.newTodo.status = data.status != '' ? data.status : 'В работе';
      this.newTodo.description = data.description;
      this.newTodo.tags = data.tags;
    },
    save() {
      let todos = JSON.parse(localStorage.getItem('Todos'));
      if (this.newTodo.id != '') {
        if (this.newTodo.date != 'Нет') {
          this.newTodo.date = moment(this.newTodo.date).format('DD.MM.YYYY HH:mm');
        }
        todos.forEach((item) => {
          if (item.id == this.newTodo.id) {
            item.title = this.newTodo.title;
            item.description = this.newTodo.description;
            item.tags = this.newTodo.tags;
            item.status = this.newTodo.status;
            item.date = this.newTodo.date;
          }
        })
      } else {
        if (this.newTodo.date != 'Нет') {
          this.newTodo.date = moment(this.newTodo.date).format('DD.MM.YYYY HH:mm');
        }
        this.newTodo.id = uuidv4();
        
        if (todos) {
          todos.push(this.newTodo);
        } else {
          todos = [this.newTodo];
        }
      }
      localStorage.setItem('Todos', JSON.stringify(todos));
      this.$emit('getTodos', {});
      this.close();
    },
    close() {
      this.dlgEdit.classList.remove('show');
      this.dlgEdit.classList.remove('open-modal');  
      document.getElementById('modal-background').classList.remove('modal-background');
    },
  },
  mounted() {
    this.dlgEdit = document.getElementById('dlgEdit');
  }
}
</script>