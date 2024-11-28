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
            <button v-if="task.status !== 'COMPLETED'" class="btn-update" @click="advanceTask(task)">
              Avanzar estado
            </button>
            <button class="btn-delete" @click="deleteTask(task.id)">Eliminar</button>
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
  
  // Avanzar el estado de la tarea
  const advanceTask = async (task) => {
    try {
      const nextStatus = task.status === 'PENDING' ? 'IN_PROGRESS' : 'COMPLETED';
      const updatedTask = { ...task, status: nextStatus };
      await $fetch(`http://127.0.0.1:8000/api/tasks/${task.id}/`, {
        method: 'PUT',
        body: JSON.stringify(updatedTask),
        headers: { 'Content-Type': 'application/json' },
      });
      fetchTasks();
    } catch (error) {
      console.error('Error al actualizar la tarea:', error);
    }
  };
  
  // Eliminar tarea
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
  
  // Formatear fecha
  const formatDate = (date) => new Date(date).toLocaleString();
  
  // Formatear texto del estado
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
    background-color: #f9f9f9;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  }
  
  .title {
    text-align: center;
    font-size: 24px;
    color: #333;
    margin-bottom: 20px;
  }
  
  .task-form {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-bottom: 30px;
  }
  
  .task-form input,
  .task-form textarea,
  .task-form select {
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 16px;
  }
  
  .task-form .btn-add {
    padding: 10px;
    background-color: #4caf50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 16px;
    transition: background-color 0.3s ease;
  }
  
  .task-form .btn-add:hover {
    background-color: #45a049;
  }
  
  .task-list {
    list-style: none;
    padding: 0;
  }
  
  .task-item {
    padding: 15px;
    margin-bottom: 15px;
    background-color: #fff;
    border: 1px solid #ddd;
    border-radius: 4px;
    box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
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
    color: white;
  }
  
  .status.pending {
    background-color: #f0ad4e;
  }
  
  .status.in_progress {
    background-color: #5bc0de;
  }
  
  .status.completed {
    background-color: #5cb85c;
  }
  
  .task-meta {
    font-size: 14px;
    color: #555;
    margin: 10px 0;
  }
  
  .task-actions {
    display: flex;
    gap: 10px;
  }
  
  .task-actions .btn-update {
    background-color: #007bff;
    color: white;
    border: none;
    padding: 8px 12px;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  .task-actions .btn-update:hover {
    background-color: #0056b3;
  }
  
  .task-actions .btn-delete {
    background-color: #d9534f;
    color: white;
    border: none;
    padding: 8px 12px;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  .task-actions .btn-delete:hover {
    background-color: #c9302c;
  }
  </style>
  