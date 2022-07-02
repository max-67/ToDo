<template>
  <div id ='TodoList'>
    <ItemAdd
    @getTodos='getTodos'
    ref="AddComponent">
    </ItemAdd>
    <h3 style="text-align: center">Ваши задачи</h3>
    <button class="button button-green pad-left" v-on:click="addTodo">Добавить</button>
    <div class="filter">
      Фильтр:
      <select v-model="selectedFilter" class="filter-select">
        <option v-for="item in filter" :key="item.id" :value="item.value">{{item.value}}</option>
      </select>
    </div>
    <div v-if="todosWFilter.length == 0">
      <h4 style="text-align: center">Нет задач</h4>
    </div>
      <Item v-for="todo in todosWFilter"
      :key="todo.id"
      :id="todo.id"
      :title="todo.title"
      :date="todo.date"
      :status="todo.status"
      :description="todo.description"
      :tags="todo.tags"
      @getTodos="getTodos"
      @edit="edit">
      </Item>
  </div>
</template>

<script>
import moment from 'moment';
import Item from './Item.vue';
import ItemAdd from './ItemAdd.vue';

export default {
  name: 'TodoList',
  components: {
    Item,
    ItemAdd
  },
  data() {
    return {
      todos: [],
      todosWFilter: [],
      newTodo: {
        id: '',
        title: '',
        date: '',
        status: 'В работе',
        description: '',
        tags: ''
      },
      filter: [
        {
          id: 1,
          value: 'Все'
        },
        {
          id: 2,
          value: 'В работе'
        },
        {
          id: 3,
          value: 'Выполнено'
        },
        {
          id: 4,
          value: 'Просрочено'
        }
      ],
      selectedFilter: '',
      editElement: ''
    }
  },
  watch: {
    selectedFilter: function () {
      this.selectFilter();
    }
  },
  methods: {
    edit(data) {
      this.newTodo.id = data.id;
      this.newTodo.title = data.title;
      this.newTodo.status = data.status;
      this.newTodo.description = data.description;
      this.newTodo.date = data.date != 'Нет' ? new Date(moment(data.date, 'DD.MM.YYYY HH:mm')) : 'Нет';
      this.newTodo.tags = data.tags;
      this.addTodo('edit');
      this.$refs.AddComponent.preparing(this.newTodo);
    },
    selectFilter() {
      if (this.selectedFilter == 'Все') {
        this.todosWFilter = this.todos;
        return;
      }
      this.todosWFilter = this.todos.filter((item) => 
        item.status == this.selectedFilter
      )
    },
    addTodo(mode) {
      if (mode != 'edit') {
        this.newTodo.id = '';
        this.newTodo.title = '';
        this.newTodo.date = 'Нет';
        this.newTodo.status = 'В работе';
        this.newTodo.description = '';
        this.newTodo.tags = '';
        this.$refs.AddComponent.preparing(this.newTodo);
      }
      this.editElement.classList.add("show");
      this.editElement.classList.add("open-modal");
      document.getElementById('modal-background').classList.add('modal-background');
    },
    getTodos() {
      const todos_in_LS = localStorage.getItem('Todos');
      this.todos = JSON.parse(todos_in_LS);
      this.selectFilter();
    },
  },
  mounted() {
    this.getTodos();
    this.editElement = document.getElementById('dlgEdit');
    setInterval(() =>{
      const date_now = new Date();
      let flag = false;
      this.todos.forEach((item) => {
        if (moment(item.date, 'DD.MM.YYYY HH:mm').isBefore(date_now) && (item.status != 'Просрочено' || item.status != 'Выполнено')) {
          item.status = 'Просрочено';
          flag = true;
          localStorage.setItem('Todos', JSON.stringify(this.todos));
        }
      });
      if (flag) {
        this.getTodos();
      }
    },30000);
    this.selectedFilter = 'Все';
  }
}
</script>