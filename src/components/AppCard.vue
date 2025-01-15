<template>
  <vue-draggable-next
      :animation="200"
      class="draggable-container d-flex"
  >
    <div
        v-for="status in statuses"
        :key="status"
        class="card task-column"
        :data-status="status"
    >
      <div class="card-title d-flex">
        <h2>{{ getStatusTitle(status) }}</h2>
        <button
            type="button"
            class="toggle-card"
            @click="toggleCardVisibility(status)"
            title="Скрити"
            aria-label="Скрити"
        >
          <span>
            <svg
                aria-hidden="true"
                width="35"
                height="35"
                viewBox="0 0 24 24"
            >
              <path
                  fill="currentcolor"
                  d="M8.062 11 6.5 9.914A1 1 0 0 1 7.914 8.5l2.616 2.616c.28.167.47.5.47.884s-.19.717-.47.884L7.914 15.5A1 1 0 1 1 6.5 14.086L8.062 13h-3.68c-.487 0-.882-.448-.882-1s.395-1 .882-1zm5.408 1.884c-.28-.167-.47-.5-.47-.884s.19-.717.47-.884L16.086 8.5A1 1 0 0 1 17.5 9.914L15.938 11h3.68c.487 0 .882.448.882 1s-.395 1-.882 1h-3.68l1.562 1.086a1 1 0 0 1-1.414 1.414z"
              ></path>
            </svg>
          </span>
        </button>
        <button
            type="button"
            class="menu-button"
            @click="toggleMenu(status)"
            title="додаткові дії"
            aria-controls="dropdownList"
            aria-haspopup="true"
            :aria-expanded="isMenuVisible[status] ? 'true' : 'false'"
        >
          ⋮
        </button>
        <div
            v-if="isMenuVisible[status]"
            class="dropdown-menu"
            id="dropdownList"
            role="menu"
        >
          <ul>
            <li>
              <button class="btn-link" type="button" @click="editColumn(status)">
                Згорнути колонку
              </button>
            </li>
            <li>
              <button class="btn-link" type="button" @click="removeColumn(status)">
                Видалити
              </button>
            </li>
            <li>
              <button class="btn-link" type="button" @click="createColumn(status)">
                Створити нову картку з завданням
              </button>
            </li>
          </ul>
        </div>
      </div>
      <div class="card-content" v-if="isCardVisible[status]">
        <div v-if="filteredTasks(status).length === 0" class="no-tasks">
          Поки що тут пусто, повертайтесь пізніше :)
        </div>
        <vue-draggable-next
            :list="groupedTasks[status]"
            :group="'tasks'"
            :animation="200"
            @end="onDragEnd"
        >
          <div
              v-for="element in groupedTasks[status]"
              :key="element.id"
              class="card-item d-flex"
              :class="{ done: element.completed }"
              :data-task-id="element.id"
          >
            <span>{{ element.title }}</span>
            <div class="actions d-flex">
              <button
                  class="edit-card"
                  @click="editTask(element)"
                  title="Редагувати"
              >
                <svg
                    aria-hidden="true"
                    width="16"
                    height="16"
                    viewBox="0 0 512 512"
                >
                  <path
                      d="M471.6 21.7c-21.9-21.9-57.3-21.9-79.2 0L362.3 51.7l97.9 97.9 30.1-30.1c21.9-21.9 21.9-57.3 0-79.2L471.6 21.7zm-299.2 220c-6.1 6.1-10.8 13.6-13.5 21.9l-29.6 88.8c-2.9 8.6-.6 18.1 5.8 24.6s15.9 8.7 24.6 5.8l88.8-29.6c8.2-2.7 15.7-7.4 21.9-13.5L437.7 172.3 339.7 74.3 172.4 241.7zM96 64C43 64 0 107 0 160L0 416c0 53 43 96 96 96l256 0c53 0 96-43 96-96l0-96c0-17.7-14.3-32-32-32s-32 14.3-32 32l0 96c0 17.7-14.3 32-32 32L96 448c-17.7 0-32-14.3-32-32l0-256c0-17.7 14.3-32 32-32l96 0c17.7 0 32-14.3 32-32s-14.3-32-32-32L96 64z"
                  />
                </svg>
              </button>
              <button
                  class="complete-card"
                  @click="toggleComplete(element)"
                  title="Виконано"
              >
                <svg
                    aria-hidden="true"
                    width="16"
                    height="16"
                    viewBox="0 0 512 512"
                    fill="#28a745"
                >
                  <path
                      d="M173.9 439.4 7.5 273c-10-10-10-26.2 0-36.2l36.2-36.2c10-10 26.2-10 36.2 0L192 312.7 432.1 72.6c10-10 26.2-10 36.2 0l36.2 36.2c10 10 10 26.2 0 36.2L210.1 439.4c-10 10-26.2 10-36.2 0z"
                  />
                </svg>
              </button>
              <button
                  class="remove-card"
                  @click="removeTask(element)"
                  title="Видалити"
              >
                <svg
                    aria-hidden="true"
                    width="14"
                    height="14"
                    viewBox="0 0 448 512"
                    fill="#e60000"
                >
                  <path
                      d="M135.2 17.7L128 32 32 32C14.3 32 0 46.3 0 64S14.3 96 32 96l384 0c17.7 0 32-14.3 32-32s-14.3-32-32-32l-96 0-7.2-14.3C307.4 6.8 296.3 0 284.2 0L163.8 0c-12.1 0-23.2 6.8-28.6 17.7zM416 128L32 128 53.2 467c1.6 25.3 22.6 45 47.9 45l245.8 0c25.3 0 46.3-19.7 47.9-45L416 128z"
                  />
                </svg>
              </button>
            </div>
          </div>
        </vue-draggable-next>
        <button type="button" class="add-card" @click="addTask(status)">
          + Додати задачу
        </button>
      </div>
    </div>
  </vue-draggable-next>
  <AppModal
      v-if="editingTask"
      v-model:task="editingTask"
      @save="saveTask"
      @close="closeModal"
  />
</template>

<script setup>
import {computed, ref} from 'vue';
import { VueDraggableNext } from 'vue-draggable-next';
import AppModal from '@/components/AppModal.vue';

const tasks = ref([
  { id: 1, title: 'Задача 1', completed: false, status: 'todo' },
  { id: 2, title: 'Задача 2', completed: false, status: 'todo' },
  { id: 3, title: 'Задача 3', completed: false, status: 'inProgress' },
  { id: 4, title: 'Задача 4', completed: true, status: 'done' },
]);

const statuses = ['todo', 'inProgress', 'done'];

const groupedTasks = computed(() => {
  return statuses.reduce((acc, status) => {
    acc[status] = tasks.value.filter((task) => task.status === status);
    return acc;
  }, {});
});

const onDragEnd = (event) => {
  const movedTask = event.item.dataset.taskId;
  const newStatus = event.to.closest(".task-column").dataset.status;
  const task = tasks.value.find((t) => t.id === parseInt(movedTask, 10));

  if (task && newStatus) {
    task.status = newStatus;
  }
};

const isCardVisible = ref({
  todo: true,
  inProgress: true,
  done: true,
});

const isMenuVisible = ref({
  todo: false,
  inProgress: false,
  done: false,
});

const editingTask = ref(null);

const filteredTasks = (status) => {
  return tasks.value.filter((task) => task.status === status);
};

const addTask = (status) => {
  editingTask.value = { id: null, title: '', completed: false, status };
};

const saveTask = (task) => {
  if (task.id === null) {
    task.id = Date.now();
    tasks.value.push(task);
  } else {
    const index = tasks.value.findIndex((t) => t.id === task.id);
    if (index !== -1) tasks.value[index] = task;
  }
  editingTask.value = null;
};

const removeTask = (task) => {
  tasks.value = tasks.value.filter((t) => t.id !== task.id);
};

const toggleComplete = (task) => {
  task.completed = !task.completed;
  task.status = task.completed ? 'done' : 'todo';
};

const editTask = (task) => {
  editingTask.value = { ...task };
};

const toggleCardVisibility = (status) => {
  isCardVisible.value[status] = !isCardVisible.value[status];
};

const toggleMenu = (status) => {
  isMenuVisible.value[status] = !isMenuVisible.value[status];
};

const editColumn = (status) => {
  toggleCardVisibility(status);
  toggleMenu(status);
};

const removeColumn = (status) => {
  if (tasks.value.filter((task) => task.status === status).length > 0) {
    tasks.value = tasks.value.filter((task) => task.status !== status);
  }
  isCardVisible.value[status] = false;
  isMenuVisible.value[status] = false;
};

const createColumn = (status) => {
  addTask(status);
  toggleMenu(status);
};

const closeModal = () => {
  editingTask.value = null;
};

const getStatusTitle = (status) => {
  const titles = {
    todo: 'TODO',
    inProgress: 'In Progress',
    done: 'Done',
  };
  return titles[status] || status;
};
</script>

<style scoped>

.draggable-container {
  flex-wrap: wrap;
  gap: 16px;
  padding: 16px;
  justify-content: center;
  align-items: flex-start;
  cursor: grab;
}

.card {
  min-width: 300px;
  background: var(--background);
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 16px;
  display: flex;
  flex-direction: column;
  flex-shrink: 0;
  position: relative;
  border: 1px solid #ccc;
}

.card-content {
  padding-top: 16px;
}

.card-title {
 align-items: center;
}

.card-title h2 {
  font-size: 18px;
  font-weight: bold;
  flex-grow: 2;
}

.card-item {
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
  padding: 8px;
  background: var(--white);
  border: 1px solid #dfe1e6;
  border-radius: 4px;
  cursor: grab;
}

.card-item.done {
  background: #e0e0e0;
  text-decoration: line-through;
  color: #6c757d;
}

.actions {
  gap: 8px;
}

.edit-card,
.complete-card,
.remove-card {
  background: none;
  border: none;
  font-size: 16px;
  cursor: pointer;
  width: 25px;
  height: 25px;

  &:hover {
    background-color: #f9f9f9;
  }
}

.add-card {
  background: none;
  border: none;
  color: #0052cc;
  font-size:  var(--14font-size);
  font-weight: bold;
  cursor: pointer;
  margin-top: 16px;
  text-align: left;
}

.add-card:hover {
  text-decoration: underline;
}

.toggle-card {
  background: none;
  border: none;
  color: var(--toggle-card);
  font-size: var(--14font-size);
  cursor: pointer;
  margin-left: 10px;
  font-weight: normal;
}

.toggle-card:hover,
.menu-button:hover {
  background-color: var(--toggle-card-hover);
  border-radius: 5px;
}

.menu-button {
  background: none;
  border: none;
  color: var(--toggle-card);
  font-size: 24px;
  cursor: pointer;
  padding: 0 15px;
}

.dropdown-menu {
  position: absolute;
  right: 10px;
  top: 40px;
  background: white;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  width: 200px;
  padding: 10px 0;
  list-style: none;
  z-index: 10;
  font-size: 14px;
}

.dropdown-menu{
  font-weight: bold;
  padding: 10px;
  background-color: #f5f5f5;
  border-bottom: 1px solid #ddd;
}

.dropdown-menu ul {
  padding: 0;
  margin: 0;
}

.dropdown-menu li {
  padding: 10px;
  cursor: pointer;
  list-style-type: none;
  font-weight: 400;
}

.dropdown-menu li:not(:last-child) {
  border-bottom: 1px solid #6c757d;
  padding-bottom: 5px;
  margin-bottom: 5px;
}

.btn-link {
  text-align: left;
}

.btn-link:hover {
  text-decoration: underline;
}

.btn-link:active,
.btn-link:focus {
  color: var(--blue);
}

.no-tasks {
  color: #888;
  font-size: var(--14font-size);
  text-align: center;
  padding: 10px;
}

</style>