
<template>
    <v-container fluid>
      <!-- Botón de nueva tarea -->
      <v-row class="mb-4">
        <v-col cols="12" class="text-end">
          <v-btn color="primary" @click="openNewTaskDialog">+ Nueva tarea</v-btn>
        </v-col>
      </v-row>
  
      <!-- Diálogo para crear/editar tarea -->
      <v-dialog v-model="dialog" max-width="500px">
        <v-form ref="form">
          <v-card>
            <v-card-title class="text-h6">
              {{ isEditing ? 'Editar tarea' : 'Crear nueva tarea' }}
            </v-card-title>
            <v-card-text>
              <v-text-field v-model="newTask.title" label="Título" :rules="[required]" />
              <v-textarea v-model="newTask.description" label="Descripción" :rules="[required]" />
              <v-select
                v-model="newTask.priority"
                label="Prioridad"
                :rules="[required]"
                :items="['LOW', 'MEDIUM', 'HIGH']"
              />
              <v-text-field
                v-model="newTask.dueDate"
                label="Fecha límite"
                type="date"
                :rules="[required]"
              />
            </v-card-text>
            <v-card-actions>
              <v-btn color="primary" @click="saveTask">
                {{ isEditing ? 'Guardar cambios' : 'Crear' }}
              </v-btn>
              <v-btn variant="text" @click="closeDialog">Cancelar</v-btn>
            </v-card-actions>
          </v-card>
        </v-form>
      </v-dialog>
  
      <v-dialog v-model="confirmDeleteDialog" max-width="400px">
        <v-card>
          <v-card-title class="text-h6">¿Eliminar tarea?</v-card-title>
          <v-card-text>¿Está seguro de que quieres eliminar esta tarea?</v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn text @click="confirmDeleteDialog = false">Cancelar</v-btn>
            <v-btn color="red" text @click="deleteTask">Eliminar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  
      <!-- Kanban columns -->
      <v-row>
        <v-col
          v-for="status in statuses"
          :key="status.value"
          cols="12"
          md="4"
          class="kanban-column"
        >
          <v-card class="pa-2">
            <v-card-title class="text-h6 font-weight-bold">
              {{ status.label }}
            </v-card-title>
            <v-divider></v-divider>
            <draggable
              :list="tasksByStatus[status.value]"
              group="tasks"
              item-key="id"
              :tag="'div'"
              :data-status="status.value"
              style="min-height: 100px"
              @end="onDrop"
            >
              <template #item="{ element }">
                <v-card class="ma-2 pa-3" elevation="4">
                  <div class="d-flex justify-space-between align-center mb-2">
                    <div class="text-h6">{{ element.title }}</div>
                    <div>
                      <v-icon icon="mdi-pencil" size="20" class="mr-2" @click="editTask(element)" />
                      <v-icon icon="mdi-delete" size="20" color="red" @click="confirmDelete(element)" />
                    </div>
                  </div>
                  <div class="text-body-2 mb-1">{{ element.description }}</div>
                  <div class="text-caption mb-1">Fecha límite: {{ element.dueDate }}</div>
                  <v-chip
                    class="mt-1"
                    size="small"
                    :color="getPriorityColor(element.priority)"
                    text-color="white"
                  >
                    {{ getPriorityLabel(element.priority) }}
                  </v-chip>
                </v-card>
              </template>
            </draggable>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </template>
  
  <script setup>
  import { ref, onMounted, computed } from 'vue'
  import draggable from 'vuedraggable'
  import api from '@/services/api'
  
  const tasks = ref([])
  const dialog = ref(false)
  const isEditing = ref(false)
  const editedTaskId = ref(null)
  const form = ref(null)
  const confirmDeleteDialog = ref(false)
  const taskToDelete = ref(null)
  
  const required = v => !!v || 'Este campo es obligatorio'
  
  const newTask = ref({
    title: '',
    description: '',
    priority: 'MEDIUM',
    dueDate: '',
    status: 'PENDING'
  })
  
  const statuses = [
    { label: 'Pendiente', value: 'PENDING' },
    { label: 'En progreso', value: 'IN_PROGRESS' },
    { label: 'Completado', value: 'COMPLETED' }
  ]
  
  const tasksByStatus = computed(() => {
    return {
      PENDING: tasks.value.filter(t => t.status === 'PENDING'),
      IN_PROGRESS: tasks.value.filter(t => t.status === 'IN_PROGRESS'),
      COMPLETED: tasks.value.filter(t => t.status === 'COMPLETED'),
    }
  })
  
  const fetchTasks = async () => {
    const token = localStorage.getItem('token')
    const { data } = await api.get('/tasks', {
      headers: { Authorization: `Bearer ${token}` }
    })
    tasks.value = data
  }
  
  const openNewTaskDialog = () => {
    resetForm()
    dialog.value = true
  }
  
  const editTask = (task) => {
    isEditing.value = true
    editedTaskId.value = task.id
    newTask.value = {
    ...task,
    dueDate: task.dueDate?.split('T')[0]
  }
    dialog.value = true
  }
  
  const saveTask = async () => {
    const result = await form.value.validate()
    if (!result.valid) return
  
    const token = localStorage.getItem('token')
  
    if (isEditing.value) {
      await api.put(`/tasks/${editedTaskId.value}`, newTask.value, {
        headers: { Authorization: `Bearer ${token}` }
      })
      const index = tasks.value.findIndex(t => t.id === editedTaskId.value)
      tasks.value[index] = { ...newTask.value, id: editedTaskId.value }
    } else {
      const { data } = await api.post('/tasks', newTask.value, {
        headers: { Authorization: `Bearer ${token}` }
      })
      tasks.value.push(data)
    }
  
    closeDialog()
  }
  
  const closeDialog = () => {
    dialog.value = false
    resetForm()
  }
  
  const resetForm = () => {
    isEditing.value = false
    editedTaskId.value = null
    newTask.value = {
      title: '',
      description: '',
      priority: 'LOW',
      dueDate: new Date().toISOString().split('T')[0],
      status: 'PENDING'
    }
  }
  
  const onDrop = async (e) => {
    const movedTask = e.item.__draggable_context.element
    const newStatus = e.to.dataset.status
    movedTask.status = newStatus
  
    const token = localStorage.getItem('token')
    await api.put(`/tasks/${movedTask.id}`, movedTask, {
      headers: { Authorization: `Bearer ${token}` }
    })
  }
  
  const confirmDelete = (task) => {
    taskToDelete.value = task
    confirmDeleteDialog.value = true
  }
  
  const deleteTask = async () => {
    const token = localStorage.getItem('token')
    await api.delete(`/tasks/${taskToDelete.value.id}`, {
      headers: { Authorization: `Bearer ${token}` }
    })
    tasks.value = tasks.value.filter(t => t.id !== taskToDelete.value.id)
    confirmDeleteDialog.value = false
    taskToDelete.value = null
  }
  
  const getPriorityLabel = (priority) => {
    switch (priority) {
      case 'LOW': return 'Bajo'
      case 'MEDIUM': return 'Medio'
      case 'HIGH': return 'Alto'
      default: return priority
    }
  }
  
  const getPriorityColor = (priority) => {
    switch (priority) {
      case 'LOW': return 'blue'
      case 'MEDIUM': return 'orange'
      case 'HIGH': return 'red'
      default: return 'grey'
    }
  }
  
  onMounted(() => {
    fetchTasks()
  })
  </script>
  
  <style scoped>
  .kanban-column {
    min-height: 80vh;
    background-color: #f9f9f9;
    border-radius: 8px;
  }
  </style>
  