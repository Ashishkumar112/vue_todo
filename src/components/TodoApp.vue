<template>
  <div class="container" style="max-width: 600px">
    <!-- Heading -->
    <h2 class="text-center mt-5">My Vue Todo App</h2>

    <!-- Input -->
    <div class="d-flex mt-5">
      <input
        type="text"
        v-model="task"
        placeholder="Enter task"
        class="w-100 form-control"
      />
      <button class="btn btn-warning rounded-0" @click="submitTask">
        SUBMIT
      </button>
    </div>

    <!-- Task table -->
    <table class="table table-bordered mt-5">
      <thead>
        <tr>
          <th scope="col">Task</th>
          <th scope="col">Date of Creation</th>
          <th scope="col" style="width: 120px">Status</th>
          <th scope="col" class="text-center">Delete</th>
          <th scope="col" class="text-center">Edit</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td>
            <span :class="{ 'line-through': task.status === 'finished' }">
              {{ task.name }}
            </span>
          </td>
          <td>
            <p class="small text-muted">{{ task.createdAt }}</p>
          </td>
          <td>
            <span
              class="pointer noselect"
              @click="changeStatus(index)"
              :class="{
                'text-danger': task.status === 'todo',
                'text-success': task.status === 'finished',
                'text-warning': task.status === 'inprogress',
              }"
            >
              {{ capitalizeFirstChar(task.status) }}
            </span>
          </td>
          <td class="text-center">
            <div @click="deleteTask(index)">
              <span class="fa fa-trash pointer"></span>
            </div>
          </td>
          <td class="text-center">
            <div @click="editTask(index)">
              <p class="fa fa-pen pointer"></p>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      task: "",
      editedTask: null,
      statuses: ["todo", "inprogress", "finished"],
      tasks: this.getSavedTasks() || [],
    };
  },

  methods: {
    capitalizeFirstChar(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },

    changeStatus(index) {
      let currentIndex = this.statuses.indexOf(this.tasks[index].status);
      currentIndex = (currentIndex + 1) % this.statuses.length;
      this.tasks[index].status = this.statuses[currentIndex];
      this.saveTasks();
    },

    submitTask() {
      if (this.task.length === 0) return;

      if (this.editedTask != null) {
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      } else {
        const currentDate = new Date().toLocaleString();
        this.tasks.push({
          name: this.task,
          status: "todo",
          createdAt: currentDate,
        });
      }

      this.saveTasks();
      this.task = "";
    },

    getSavedTasks() {
      const savedTasks = localStorage.getItem("tasks");
      return savedTasks ? JSON.parse(savedTasks) : null;
    },

    saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },

    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },

    editTask(index) {
      this.task = this.tasks[index].name;
      this.editedTask = index;
    },
  },
};
</script>

<style scoped>
.pointer {
  cursor: pointer;
}
.noselect {
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
.line-through {
  text-decoration: line-through;
}
</style>
