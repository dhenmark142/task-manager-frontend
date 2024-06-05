<template>
  <div>
    <h1>Task Management</h1>
    <ul>
      <li v-for="task in tasks" :key="task.id">
        <input type="checkbox" v-model="task.completed" @change="updateTask(task)">
        <span :class="{ completed: task.completed }">{{ task.title }}</span>
        <button @click="deleteTask(task.id)">Delete</button>
      </li>
    </ul>
    <input v-model="newTask" @keyup.enter="addTask">
    <button @click="addTask">Add Task</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [],
      newTask: ''
    };
  },
  created() {
    this.fetchTasks();
  },
  methods: {
    fetchTasks() {
      this.$axios.get('/tasks')
        .then(response => {
          this.tasks = response.data;
        })
        .catch(error => {
          console.error('There was an error fetching tasks:', error);
        });
    },
    addTask() {
      this.$axios.post('/tasks', { title: this.newTask })
        .then(response => {
          this.tasks.push(response.data);
          this.newTask = '';
        })
        .catch(error => {
          console.error('There was an error adding the task:', error);
        });
    },
    updateTask(task) {
      this.$axios.put(`/tasks/${task.id}`, { completed: task.completed })
        .then(response => {
          Object.assign(task, response.data);
        })
        .catch(error => {
          console.error('There was an error updating the task:', error);
        });
    },
    deleteTask(id) {
      this.$axios.delete(`/tasks/${id}`)
        .then(() => {
          this.tasks = this.tasks.filter(task => task.id !== id);
        })
        .catch(error => {
          console.error('There was an error deleting the task:', error);
        });
    }
  }
};
</script>

<style>
.completed {
  text-decoration: line-through;
}
</style>
