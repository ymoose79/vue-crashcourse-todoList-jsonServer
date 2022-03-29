<template>
  <div class="container">
    <Header
      @btn-click="toggleForm"
      :toggleButton="showAddTask"
      title="here we go"
    />

    <div v-if="showAddTask">
      <AddTask @add-task="addTask" />
    </div>

    <Tasks
      @toggle-reminder="toggleReminder"
      @delete-task="deleteTask"
      :tasks="tasks"
    />
  </div>
</template>

<script>
import Header from "./components/Header";
import Tasks from "./components/Tasks";
import AddTask from "./components/AddTask";

export default {
  name: "App",
  components: {
    Header,
    Tasks,
    AddTask,
  },
  data() {
    return {
      // var defined here
      tasks: [],
      // toggle rendering of component
      showAddTask: false,
    };
  },
  methods: {
    // displays/removes form when button is clicked
    toggleForm() {
      this.showAddTask = !this.showAddTask;
    },
    // POST req to DB
    async addTask(newTask) {
      const res = await fetch("http://localhost:5000/tasks", {
        method: "POST",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(newTask),
      });
      const data = await res.json();
      this.tasks = [...this.tasks, data];
    },
    // Del req to DB
    async deleteTask(id) {
      if (confirm("are you sure?")) {
        const res = await fetch(`http://localhost:5000/tasks/${id}`, {
          method: "DELETE",
        });
        res.status === 200
          ? (this.tasks = this.tasks.filter((task) => task.id !== id))
          : alert("Error deleting task");
      }
    },
    // event coming from Task that changes class

    async toggleReminder(id) {
      // 1st grab specific task
      const taskToToggle = await this.fetchTask(id);
      // create var, spread and reset key:value
      const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

      // create PUT request
      const res = await fetch(`http://localhost:5000/tasks/${id}`, {
        method: "PUT",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(updTask),
      });

      const data = await res.json();

      // Loop (map) through tasks, find the obj.id, spread out obj and reset key:value
      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: data.reminder } : task
      );
    },
    // added once DB was created with {tasks}
    async fetchTasks() {
      const res = await fetch("http://localhost:5000/tasks");
      const data = await res.json();
      return data;
    },
    async fetchTask(id) {
      const res = await fetch(`http://localhost:5000/tasks/${id}`);
      const data = await res.json();
      return data;
    },
  },
  // created is common mount for http requests
  // remove async/await then hardcode {tasks} to get the ball rolling
  async created() {
    this.tasks = await this.fetchTasks();
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: "Poppins", sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}
.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
</style>
