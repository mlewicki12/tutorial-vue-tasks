<template>
  <div>
    <AddTask v-show='showAddTask' @add-task='addTask' />
  </div>

  <Tasks
    @delete-task='deleteTask'
    @toggle-reminder='toggleReminder'
    :tasks='tasks'
  />
</template>

<script>
  import Tasks from '../components/Tasks';
  import AddTask from '../components/AddTask';

  export default {
    name: 'Home',
    props: {
      showAddTask: Boolean
    },
    components: {
      Tasks,
      AddTask
    },
    data() {
      return {
        tasks: []
      };
    },
    methods: {
      async fetchTasks() {
        const res = await fetch('api/tasks');
        const data = await res.json();

        return data;
      },
      async fetchTask(id) {
        const res = await fetch(`api/tasks/${id}`);
        const data = await res.json();

        return data;
      },
      async addTask(task) {
        const res = await fetch('api/tasks', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(task)
        });

        const data = await res.json();
        this.tasks = [...this.tasks, data];
      },
      async deleteTask(id) {
        if(confirm('Are you sure?')) {
          const res = await fetch(`api/tasks/${id}`, {
            method: 'DELETE',
          });

          if(res.status === 200) this.tasks = this.tasks.filter(task => task.id !== id);
            else alert('Error deleting task');
        }
      },
      async toggleReminder(id) {
        const task = await this.fetchTask(id);
        const update = {...task, reminder: !task.reminder};

        const res = await fetch(`api/tasks/${id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(update)
        });

        const data = await res.json();
        this.tasks = this.tasks.map(task => task.id === id ? data : task);
      }
    },
    async created() {
      this.tasks = await this.fetchTasks();
    }
  }
</script>