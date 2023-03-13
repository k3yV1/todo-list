<template>
  <div class="todo-list__component">
    <div class="component__box">
      <div class="box__title">
        <span>Todo List</span>
      </div>
      <div class="box__task-filters">
        <button @click="filter = 'all'" :class="{ active: filter === 'all' }">
          Все
        </button>
        <button
          @click="filter = 'today'"
          :class="{ active: filter === 'today' }"
        >
          Сегодня дедлайн
        </button>
        <button @click="filter = 'done'" :class="{ active: filter === 'done' }">
          Выполнено
        </button>
      </div>
      <div class="box__task-list" v-if="tasks.length === 0" style="text-align: center">
        <span>Список задач - пуст</span>
      </div>
      <div class="box__task-list" v-else>
        <div v-for="task in filteredTasks" :key="task.id" class="task">
          <input type="checkbox" v-model="task.done" />
          <span :class="{ done: task.done }">{{ task.text }}</span>
          <span class="deadline">{{ task.deadline }}</span>
          <button class="remove-task" @click="removeTask(task.id)">x</button>
        </div>
      </div>
      <div class="box__task-add">
        <div class="task-add__input">
          <input type="text" v-model="newTask">
        </div>
        <div class="task-add__input">
          <input type="date" v-model="newDeadline">
        </div>
        <div class="task-add__btn">
          <button @click="addTask">Добавить</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data: () => ({
    tasks: JSON.parse(localStorage.getItem('tasks') || '[]'),
    newTask: "",
    newDeadline: "",
    filter: "all",
  }),

  methods: {
    addTask() {
      const task = {
        id: this.tasks.length + 1,
        text: this.newTask,
        done: false,
        deadline: this.newDeadline,
      };
      this.tasks.push(task);
      this.newTask = "";
      this.newDeadline = "";
      this.saveTasks();
    },

    removeTask(id) {
      const index = this.tasks.findIndex(task => task.id === id)
      if(index !== -1) {
        this.tasks.splice(index, 1);
        this.saveTasks();
      }
    },

    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    }
  },

  computed: {
    filteredTasks() {
      if (this.filter === "all") {
        return this.tasks;
      } else if (this.filter === "today") {
        const today = new Date().toISOString().slice(0, 10);
        return this.tasks.filter((task) => task.deadline === today);
      } else if (this.filter === "done") {
        return this.tasks.filter((task) => task.done);
      } else {
        return console.log("error");
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.todo-list__component {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  .component__box {
    background-color: #fff;
    padding: 30px;
    border-radius: 12px;
    box-shadow: 0 12px 10px -6px;

    .box__title {
      text-align: center;
      span {
        font-size: 32px;
        color: #000;
      }
    }

    .box__task-list {
      display: flex;
      flex-direction: column;
      height: 105px;
      overflow: scroll;
      gap: 10px;
      margin-top: 15px;
      .task {
        display: flex;
        justify-content: space-between;
        align-items: center;

        span {
          font-size: 18px;
          &.done {
            text-decoration: line-through;
          }
        }

        .deadline {
          margin-left: 10px;
          font-size: 0.8em;
          color: #888;
        }

        .remove-task {
          cursor: pointer;
          background-color: #fff;
          color: red;
          border: 1px solid red;
          padding: 5px;
          transition: .3s all;

          &:hover {
            background-color: rgb(240, 104, 104);
            color: #fff;
          }
        }
      }
    }

    .box__task-filters {
      margin-top: 15px;
      display: flex;
      justify-content: center;
      gap: 15px;

      button {
        cursor: pointer;
        background-color: #fff;
        color: #93acd8;
        border: 1px solid #93acd8;
        padding: 10px;
        transition: 0.3s all;

        &:hover {
          background-color: #93acd8;
          color: #fff;
        }

        &.active {
          background-color: #93acd8;
          color: #fff;
        }
      }
    }

    .box__task-add {
      display: flex;
      flex-direction: column;
      text-align: center;
      gap: 10px;
      margin-top: 15px;
      .task-add__input {
        display: flex;
        input {
          width: 100%;
          height: 30px;
          padding-left: 10px;
          border-radius: 5px;
          border: 1px solid #93acd8;
        }
      }

      .task-add__btn {
        button {
          cursor: pointer;
          padding: 10px 20px;
          background-color: #fff;
          color: #93acd8;
          border: 1px solid #93acd8;
          transition: .3s all;

          &:hover {
            background-color: #93acd8;
            color: #fff;
          }
        }
      }
    }
  }
}
</style>