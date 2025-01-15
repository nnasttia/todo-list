<template>
  <div class="modal d-flex">
    <div class="modal-content">
      <h2>{{ task.id ? 'Edit Task' : 'Create Task' }}</h2>
      <form
          @submit.prevent="save"
          class="form d-flex">
        <div class="form-group d-flex">
          <label for="title">Назва</label>
          <input
              id="title"
              type="text"
              v-model="localTask.title"
              required
              class="form-control" />
        </div>

        <div class="form-group d-flex">
          <label for="description">Опис</label>
          <textarea
              id="description"
              v-model="localTask.description"
              rows="3" required
              class="form-control">
          </textarea>
        </div>

        <div class="form-group d-flex">
          <label for="responsible">Відповідальна особа</label>
          <input
              id="responsible"
              type="text"
              v-model="localTask.responsible"
              required
              class="form-control" />
        </div>

        <div class="form-group d-flex">
          <label for="status">Статус:</label>
          <select
              id="status"
              v-model="localTask.status"
              :disabled="!isEditable"
              class="form-control"
              @change="handleStatusChange"
          >
            <option value="todo">TODO</option>
            <option value="inProgress">In Progress</option>
            <option value="done">Done</option>
          </select>
        </div>

        <div class="form-group d-flex">
          <label for="priority">Пріоритет</label>
          <select
              id="priority"
              v-model="localTask.priority"
              required
              class="form-control">
            <option>Low</option>
            <option>Medium</option>
            <option>High</option>
          </select>
        </div>

        <div class="actions d-flex">
          <button type="submit" class="btn btn-save">Зберегти</button>
          <button type="button" class="btn btn-cancel" @click="$emit('close')">Скасувати</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { reactive, watch } from 'vue';

const props = defineProps({
  task: Object,
  isEditable: {
    type: Boolean,
    default: true,
  },
});
const emit = defineEmits(['save', 'close', 'moveTask']);

const localTask = reactive({ ...props.task });

const save = () => {
  emit('save', { ...localTask });
};

const handleStatusChange = () => {
  emit('moveTask', { ...localTask });
};

watch(
    () => props.task,
    (newTask) => {
      Object.assign(localTask, newTask);
    }
);

</script>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.5);
  z-index: 1000;
}

.modal-content {
  background: white;
  padding: 20px 24px;
  border-radius: 12px;
  width: 400px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
}

h2 {
  font-size: 20px;
  margin-bottom: 16px;
  text-align: center;
}

.form {
  flex-direction: column;
  gap: 16px;
}

.form-group {
  flex-direction: column;
}

label {
  font-size:  var(--14font-size);
  font-weight: bold;
  margin-bottom: 4px;
}

.actions {
  justify-content: space-between;
}

.btn-save {
  background: #0052cc;
  color: white;
}

.btn-save:hover {
  background: #003f99;
}

.btn-cancel {
  background: #e4e6eb;
  color: #333;
}

.btn-cancel:hover {
  background: #d1d3d6;
}
</style>
