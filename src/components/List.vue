<template>
  <main class="list_main">
    <section class="list_section">
      <h1 class="main_title">
        To do list du
        {{ currentDate }}
      </h1>
      <form class="task_form" @submit.prevent="addNewTask(newTask)">
        <label class="task_form-label">Nom de la tâche :</label>
        <input
          id="test"
          class="task_form-input"
          type="text"
          v-model="newTask"
        />
        <button type="submit" class="add_button"></button>
      </form>
      <div class="list_wrapper" v-if="taskStatus.length > 0">
        <article v-for="taskStatusItem in taskStatus" class="list_article">
          <div class="listItem_wrapper">
            <i
              tabindex="0"
              v-if="!taskStatusItem.isTaskDone"
              @keyup="updateTaskStatus(taskStatusItem)"
              @click="updateTaskStatus(taskStatusItem)"
              class="fa-regular fa-circle circle_icon"
            ></i>
            <i
              tabindex="0"
              v-if="taskStatusItem.isTaskDone"
              @keyup="updateTaskStatus(taskStatusItem)"
              @click="updateTaskStatus(taskStatusItem)"
              class="fa-regular fa-circle-check check_icon"
            ></i>
            <p class="list_task" :key="taskStatusItem.task">
              {{ taskStatusItem.task }}
            </p>
          </div>
          <div>
            <i
              tabindex="0"
              @keyup="deleteTask(taskStatusItem)"
              @click="deleteTask(taskStatusItem)"
              class="fa-solid fa-trash trash_icon"
            ></i>
          </div>
        </article>
      </div>
    </section>
  </main>
</template>

<script>
export default {
  data() {
    return {
      date: new Date(),
      newTask: '',
      taskStatus: [],
    };
  },

  computed: {
    day() {
      return this.date?.getDate();
    },
    month() {
      return this.date?.getMonth() + 1;
    },
    year() {
      return this.date?.getFullYear();
    },

    currentDate() {
      const currentDay = this.day < 10 ? `0${this.day}` : this.day;
      const currentMonth = this.month < 10 ? `0${this.month}` : this.month;
      return `${currentDay}/${currentMonth}/${this.year}`;
    },
  },

  methods: {
    addNewTask(newTask) {
      const inputElement = document.querySelector('.task_form-input');
      if (inputElement.classList.contains('active')) {
        inputElement.classList.remove('active');
      }
      if (newTask.length === 0) {
        setTimeout(() => {
          inputElement.classList.add('active');
        }, 0);
        return;
      }
      if (!this.taskStatus.includes(newTask)) {
        const newTaskStatus = {
          task: newTask,
          isTaskDone: false,
        };
        this.taskStatus.push(newTaskStatus);
        localStorage.setItem('tasks', JSON.stringify(this.taskStatus));
      }
      this.newTask = '';
    },
    updateTaskStatus(taskStatusItem) {
      this.taskStatus.forEach((choosenTask) => {
        if (choosenTask.task === taskStatusItem.task) {
          choosenTask.isTaskDone = !choosenTask.isTaskDone;
          localStorage.setItem('tasks', JSON.stringify(this.taskStatus));
        }
      });
    },
    deleteTask(taskStatusItem) {
      const index = this.taskStatus.indexOf(taskStatusItem);
      this.taskStatus.splice(index, 1);
      localStorage.setItem('tasks', JSON.stringify(this.taskStatus));
    },
  },

  mounted() {
    const storedTasks = localStorage.getItem('tasks');
    if (storedTasks) {
      this.taskStatus = JSON.parse(storedTasks);
    }
  },
};
</script>

<style scoped>
.list_main {
  display: flex;
  justify-content: center;
  align-items: center;
}

.list_section {
  background-color: #fff;
  border-radius: 5px;
  padding: 32px;
}

.main_title {
  font-size: 3rem;
  margin: 0 0 48px 0;
  text-align: center;
}

.task_form {
  font-size: 1.5rem;
  display: flex;
  flex-direction: column;
  border-bottom: 1px solid #d9d9d9;
}

.task_form-input {
  margin: 8px 0 40px 0;
  border: 1px solid #d9d9d9;
  padding: 16px;
  border-radius: 5px;
  font-size: 1.2rem;
}
.task_form-input.active {
  border: 1px solid #ff4d4d;
  animation: shake 150ms ease-in-out;
}

@keyframes shake {
  0% {
    transform: translateX(0);
  }
  50% {
    transform: translateX(10px);
  }
  100% {
    transform: translateX(-10px);
  }
}

.add_button {
  height: 50px;
  width: 115px;
  box-shadow: 5px 5px 5px #d9d9d9;
  position: relative;
  transform: translate(-50%, -40%);
  top: 50%;
  left: 50%;
  border-radius: 5px;
  overflow: hidden;
  border: none;
  cursor: pointer;
}

.add_button:before {
  content: '';
  height: 150px;
  width: 200px;
  position: absolute;
  background: conic-gradient(
    #fd004c,
    #fe9000,
    #fff020,
    #3edf4b,
    #3363ff,
    #b102b7,
    #fd004c
  );
  left: -38%;
  top: -100%;
  animation: spin 1.3s infinite linear;
}

@keyframes spin {
  100% {
    transform: rotate(360deg);
  }
}
.add_button:after {
  font-family: 'Playfair Display', serif;
  content: 'Ajouter';
  position: absolute;
  background-color: #fff;
  height: 85%;
  width: 94%;
  top: 7%;
  left: 3%;
  border-radius: 5px;
  color: #333;
  font-size: 1.1rem;
  display: grid;
  place-items: center;
}

.list_wrapper {
  display: flex;
  flex-direction: column;
  gap: 16px;
  margin-top: 48px;
  border: 1px solid #b3b3b3;
  border-radius: 5px;
  padding: 24px;
}

.list_article {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #d9d9d9;
  padding: 16px 0;
}

.listItem_wrapper {
  display: flex;
  align-items: center;
}

.circle_icon,
.check_icon,
.trash_icon {
  cursor: pointer;
  padding-right: 16px;
  font-size: 1.5rem;
}

.check_icon {
  color: #26734d;
}

.trash_icon {
  color: #ff4d4d;
  text-align: end;
  padding: 0 0 0 16px;
}

.list_task {
  font-size: 1.2rem;
  margin: 0;
}

@media (max-width: 768px) {
  .main_title {
    font-size: 2rem;
  }

  .task_form-label {
    text-align: center;
  }
}

@media (max-width: 480px) {
  .list_section {
    padding: 16px;
  }
}
</style>
