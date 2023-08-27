<template>
  <div class="todoapp">
    <div class="content">
      <h2 class="todoapp__title">To Do List</h2>

      <div v-if="editTask === null" class="todoapp__input">
        <input class="todoapp__inputs" type="text" v-model="task" @keyup.enter="addTask" placeholder="New Task">
      </div>
      <div v-else>
        <input class="todoapp__inputs" type="text" v-model="editTask.name" @keyup.enter="addTask" placeholder="Edit Task">
      </div>
      
      <div class="todoapp__table">
        <div class="todoapp__table-tf" v-for="(task, index) in tasks" :key="index" :class="{ 'completed': task.status === false }" id="myCheckbox">
            <div class="todoapp__table_center" @click="changeStatus(index)">
              <div class="inp" :class="{ 'activ': task.status === false }">
                <input type="checkbox" :class="{ 'activ': task.status === false }">
              </div>
              <label for="myCheckbox"></label>
              <p :class="{ 'activ1': task.status === false }">{{ task.name }}</p> 
            </div>


          <div class="todoapp__table_dalet">
            <span class="edit-icon" @click="editTask = task">
              <img src="./assets/img/red.svg" alt="pen">
            </span>
            
            <span class="delete-icon" @click="deleteTask(index)">
              <img src="./assets/img/del.svg" alt="del">
            </span>
          </div>    
        </div>
      </div>
      <div class="todoapp__buttons">
        <button @click="deleteCompletedTasks">Clear Completed</button>
        <button @click="deleteAllTasks">Clear All</button>
      </div>
      <p class="count">Remaining Tasks: {{ remainingTasks }}</p>
    </div>
    
    
    
    
    <div class="dd"></div>
  </div>
</template>


<script>
import { ref, computed, watch} from 'vue';

export default {
  setup() {
    const task = ref('');
    const editTask = ref(null);
    const status = [true, false];
    const tasks = ref([]);

    const changeStatus = (index) => {
      
      console.log(tasks.value[index]);
      if (tasks.value[index].status === true) {
        tasks.value[index].status = false;
      } else if (tasks.value[index].status === false) {
        tasks.value[index].status = true;
      }
      saveTasks();
    };

    const deleteTask = (index) => {
      tasks.value.splice(index, 1);
      saveTasks();
    };

    const addTask = () => {
      if (editTask.value) {
        editTask.value = null;
      } else if (task.value.trim() !== '') {
        tasks.value.push({
          name: task.value,
          status: true,
        });
        task.value = '';
      }
      editTask.value = null;
      saveTasks();
    };

    const deleteCompletedTasks = () => {
      tasks.value = tasks.value.filter((task) => task.status !== false);
      saveTasks();
    };

    const deleteAllTasks = () => {
      tasks.value = [];
      saveTasks();
    };

    const finishEditing = (task) => {
      if (task.name.trim() !== '') {
        editTask.value = null;
      } else {
        deleteTask(tasks.value.indexOf(task));
      }
      saveTasks();
    };

    const remainingTasks = computed(() => {
      return tasks.value.filter((task) => task.status === true).length;
    });

    const saveTasks = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value));
    };

    watch(
      () => tasks.value,
      () => {
        saveTasks();
      },
      { deep: true }
    );

    const loadTasks = () => {
      const savedTasks = localStorage.getItem('tasks');
      tasks.value = savedTasks ? JSON.parse(savedTasks) : [];
    };

    loadTasks();

    return {
      task,
      editTask,
      tasks,
      changeStatus,
      deleteTask,
      addTask,
      deleteCompletedTasks,
      deleteAllTasks,
      finishEditing,
      remainingTasks,
    };
  },
};
</script>