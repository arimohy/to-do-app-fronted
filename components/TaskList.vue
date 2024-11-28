<!-- <template>
    <ul>
      <li v-for="task in tasks" :key="task.id">
        <p>{{ task.title }} - {{ task.status }}</p>
        <button @click="edit(task)">Editar</button>
        <button @click="remove(task.id)">Eliminar</button>
      </li>
    </ul>
  </template>
  
  <script>
  export default {
    props: {
      tasks: Array
    },
    methods: {
      edit(task) {
        this.$emit('edit-task', task);
      },
      remove(taskId) {
        this.$emit('delete-task', taskId);
      }
    }
  };
  </script>
   -->