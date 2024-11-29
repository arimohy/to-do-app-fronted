<template>
    <div class="todo-app">
      <h1 class="title">Lista de Tareas</h1>
  
      <!-- Formulario para crear una nueva tarea -->
      <form class="task-form" @submit.prevent="createTask">
        <input v-model="newTask.title" placeholder="Título" required />
        <textarea v-model="newTask.description" placeholder="Descripción" required></textarea>
        <select v-model="newTask.status" required>
          <option value="PENDING">Pendiente</option>
          <option value="IN_PROGRESS">En Progreso</option>
          <option value="COMPLETED">Completada</option>
        </select>
        <button type="submit" class="btn-add">Añadir Tarea</button>
      </form>
  
      <!-- Lista de tareas -->
      <ul class="task-list">
  <li v-for="task in tasks" :key="task.id" class="task-item">
    <div v-if="!task.isEditing" class="task-view">
      <div class="task-header">
        <h3>{{ task.title }}</h3>
        <span :class="`status ${task.status.toLowerCase().replace(' ', '-')}`">
          {{ formatStatus(task.status) }}
        </span>
      </div>
      <p>{{ task.description }}</p>
      <div class="task-meta">
        <p>Creada: {{ formatDate(task.created_at) }}</p>
        <p>Última actualización: {{ formatDate(task.updated_at) }}</p>
      </div>
      <div class="task-actions">
        <button class="btn-edit" @click="startEditing(task)">Editar</button>
        <button class="btn-delete" @click="deleteTask(task._id)">Eliminar</button>
      </div>
    </div>

    <!-- Formulario de edición inline -->
    <div v-else class="task-edit">
      <input v-model="task.title" placeholder="Título" required />
      <textarea v-model="task.description" placeholder="Descripción" required></textarea>
      <select v-model="task.status" required>
        <option value="PENDING">Pendiente</option>
        <option value="IN_PROGRESS">En Progreso</option>
        <option value="COMPLETED">Completada</option>
      </select>
      <div class="task-actions">
        <button class="btn-save" @click="updateTask(task)">Guardar</button>
        <button class="btn-cancel" @click="cancelEditing(task)">Cancelar</button>
      </div>
    </div>
  </li>
</ul>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  
  // Variables reactivas
  const tasks = ref([]);
  const newTask = ref({
    title: '',
    description: '',
    status: 'PENDING',
  });
  
  // Función para obtener todas las tareas
  const fetchTasks = async () => {
    try {
      const response = await $fetch('http://127.0.0.1:8000/api/tasks/');
      tasks.value = response;
    } catch (error) {
      console.error('Error al obtener las tareas:', error);
    }
  };
  
  // Crear una nueva tarea
  const createTask = async () => {
    try {
      await $fetch('http://127.0.0.1:8000/api/tasks/', {
        method: 'POST',
        body: JSON.stringify(newTask.value),
        headers: { 'Content-Type': 'application/json' },
      });
      alert('Tarea creada exitosamente');
      fetchTasks();
    } catch (error) {
      console.error('Error al crear la tarea:', error);
    }
  };
  
const startEditing = (task) => {
  task.isEditing = true; 
};

const cancelEditing = (task) => {
  task.isEditing = false; 
  fetchTasks(); 
};


const updateTask = async (task) => {
  try {
    await $fetch(`http://127.0.0.1:8000/api/tasks/${task._id}/`, {
      method: 'PUT',
      body: JSON.stringify({
        title: task.title,
        description: task.description,
        status: task.status,
      }),
      headers: { 'Content-Type': 'application/json' },
    });
    task.isEditing = false; 
    fetchTasks();
  } catch (error) {
    console.error('Error al actualizar la tarea:', error);
  }
};

  
  const deleteTask = async (taskId) => {
    try {
      await $fetch(`http://127.0.0.1:8000/api/tasks/${taskId}/`, {
        method: 'DELETE',
      });
      fetchTasks();
    } catch (error) {
      console.error('Error al eliminar la tarea:', error);
    }
  };
  
  const formatDate = (date) => new Date(date).toLocaleString();
  
  const formatStatus = (status) => {
    switch (status) {
      case 'PENDING':
        return 'Pendiente';
      case 'IN_PROGRESS':
        return 'En Progreso';
      case 'COMPLETED':
        return 'Completada';
      default:
        return status;
    }
  };
  
  // Al montar el componente, obtener las tareas
  onMounted(() => {
    fetchTasks();
  });

  
  </script>
  
  <style scoped>
.todo-app {
  max-width: 700px;
  margin: 0 auto;
  padding: 20px;
  background: linear-gradient(145deg, #ffffff, #f0f0f0);
  border-radius: 12px;
  box-shadow: 4px 4px 8px rgba(0, 0, 0, 0.1), -4px -4px 8px rgba(255, 255, 255, 0.7);
}

.title {
  text-align: center;
  font-size: 26px;
  font-weight: bold;
  color: #4a4a4a;
  margin-bottom: 20px;
}

.task-form {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 30px;
}

.task-form input,
.task-form textarea,
.task-form select {
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
  background-color: #f7f7f7;
  box-shadow: inset 2px 2px 4px rgba(0, 0, 0, 0.1);
  transition: border-color 0.3s ease;
}

.task-form input:focus,
.task-form textarea:focus,
.task-form select:focus {
  border-color: #4caf50;
  outline: none;
}

.task-form .btn-add {
  padding: 12px;
  background: linear-gradient(145deg, #4caf50, #43a047);
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
  transition: transform 0.2s, box-shadow 0.2s;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
}

.task-form .btn-add:hover {
  transform: translateY(-2px);
  box-shadow: 2px 4px 8px rgba(0, 0, 0, 0.2);
}

.task-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.task-item {
  padding: 15px;
  margin-bottom: 15px;
  background: linear-gradient(145deg, #ffffff, #f0f0f0);
  border-radius: 10px;
  box-shadow: 2px 2px 6px rgba(0, 0, 0, 0.1), -2px -2px 6px rgba(255, 255, 255, 0.7);
  transition: transform 0.2s;
}

.task-item:hover {
  transform: scale(1.02);
}

.task-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.status {
  padding: 5px 10px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: bold;
  text-transform: uppercase;
  color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.status.pending {
  background-color: #ff9800;
}

.status.in_progress {
  background-color: #03a9f4;
}

.status.completed {
  background-color: #4caf50;
}

.task-meta {
  font-size: 14px;
  color: #666;
  margin-top: 10px;
}

.task-actions {
  display: flex;
  gap: 10px;
  margin-top: 15px;
}

.task-actions button {
  border: none;
  border-radius: 8px;
  padding: 8px 12px;
  cursor: pointer;
  font-size: 14px;
  font-weight: bold;
  transition: transform 0.2s, box-shadow 0.2s;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
}

.task-actions .btn-edit {
  background: linear-gradient(145deg, #2196f3, #1e88e5);
  color: white;
}

.task-actions .btn-edit:hover {
  transform: translateY(-2px);
}

.task-actions .btn-delete {
  background: linear-gradient(145deg, #f44336, #e53935);
  color: white;
}

.task-actions .btn-delete:hover {
  transform: translateY(-2px);
}

.task-edit {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.task-edit button {
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 14px;
}

.btn-save {
  background: linear-gradient(145deg, #4caf50, #43a047);
  color: white;
}

.btn-cancel {
  background: linear-gradient(145deg, #9e9e9e, #757575);
  color: white;
}
</style>
