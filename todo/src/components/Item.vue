<template>
  <div id="Item">
    <div class="item-content">
      <div class="title">{{this.title}}</div>
      Описание: {{this.description}}
      <br>
      Дедлайн: {{this.date}}
      <br>
      Статус: {{this.status}}
      <button class="button-sm button-green" v-if="this.status == 'В работе'" v-on:click="done">Выполнен</button>
      <br>
    </div>
      <div class="item-buttons"> 
        <button class="button-sm button-grey" v-on:click="edit">Редактировать</button>
        <button class="button-sm button-red pad-top" v-on:click="remove">Удалить</button>
      </div>
  </div>
</template>

<script>
export default {
  props: [
    'id',
    'title',
    'date',
    'status',
    'tags',
    'description',
  ],
  name: 'item',
  methods: {
    edit() {
       this.$emit('edit', {
        id: this.id,
        title: this.title,
        date: this.date,
        status: this.status,
        tags: this.tags,
        description: this.description
       });
    },
    remove() {
      const todos_in_LS = JSON.parse(localStorage.getItem('Todos'));
      todos_in_LS.forEach((i, index) => {
        if (i.id == this.id) {
          todos_in_LS.splice(index, 1);
          return;
        }
      })
      localStorage.setItem('Todos', JSON.stringify(todos_in_LS));
      this.$emit('getTodos', {});
    },
    done() {
      const todos_in_LS = JSON.parse(localStorage.getItem('Todos'));
      todos_in_LS.forEach((i) => {
        if (i.id == this.id) {
          i.status = 'Выполнено';
          return;
        }
      })
      localStorage.setItem('Todos', JSON.stringify(todos_in_LS));
      this.$emit('getTodos', {});
    }
  },
  
  mounted() {
  }
}
</script>

<style>

</style>