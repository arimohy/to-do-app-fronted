<!-- <template>
    <form @submit.prevent="submitTask">
      <input v-model="task.title" placeholder="Título" required />
      <input v-model="task.description" placeholder="Descripción" required />
      <button type="submit">Agregar tarea</button>
    </form>
  </template>
  
  <script>
  export default {
    data() {
      return {
        task: {
          title: '',
          description: '',
          status: 'Pendiente'
        }
      };
    },
    methods: {
      submitTask() {
        this.$emit('add-task', this.task);
        this.task.title = '';
        this.task.description = '';
      }
    }
  };
  </script>
   -->