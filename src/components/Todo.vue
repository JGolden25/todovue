<script>
const STORAGE_KEY = 'todo-storage';
export default {
  name: 'Todo',
  props: {
    msg: String,
  },
  el: '#statusFilter',
  data() {
    return {
      task: '',
      editedTask: null,
      statuses: ['incomplete', 'complete'],
      status2: '',
      filter: 'all',
      /* Status could be: 'incomplete' / 'complete' */
      tasks: [
        {
          name: 'Beat Pokémon Legends: Arceus',
          status: 'incomplete',
        },
        {
          name: 'Get a Steamdeck',
          status: 'incomplete',
        },
        {
          name: 'Create this todo app',
          status: 'complete',
        },
      ],
    };
  },
  computed: {
    tasks: function () {
      let filterType = this.status2;
      return this.tasks.filter(function (item) {
        let filtered = true;
        if (filterType && filterType.length > 0) {
          filtered = item.status == filterType;
        }

        return filtered;
      });
    },
  },
  created() {
    this.tasks = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
  },
  methods: {
    /**
     * Capitalize first character
     */
    capitalizeFirstChar(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
    /**
     * Change status of task by index
     */
    changeStatus(index) {
      let newIndex = this.statuses.indexOf(this.tasks[index].status);
      if (++newIndex > 2) newIndex = 0;
      this.tasks[index].status = this.statuses[newIndex];
    },

    /**
     * Deletes task by index
     */
    deleteTask(index) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks));
      this.tasks.splice(index, 1);
    },
    /**
     * Edit task
     */
    editTask(index) {
      this.task = this.tasks[index].name;
      this.editedTask = index;
    },
    /**
     * Add / Update task
     */
    submitTask() {
      if (this.task.length === 0) return;
      /* Update the task */
      if (this.editedTask != null) {
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      } else {
        /* Add new task */
        this.tasks.push({
          name: this.task,
          status: 'incomplete',
        });
      }
      this.task = '';
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks));
    },
  },
};
</script>

<template>
  <div class="container mt-5" style="max-width: 600px">
    <h2 class="text-center main-title mt-4">JCG's Vue Todo App</h2>

    <div class="d-flex mt-4 add-task">
      <input
        type="text"
        v-model="task"
        placeholder="Type New Task Here"
        class="form-control"
      />
      <button class="btn btn-sub rounded-2" @click="submitTask">Submit</button>
    </div>
    <!-- Task table -->
    <table class="table table-bordered mt-5">
      <thead>
        <tr>
          <th scope="col" class="table-text">Task</th>
          <th scope="col" class="table-text" style="width: 120px">Status</th>
          <th scope="col" class="text-center trash table-text">Delete</th>
          <th scope="col" class="text-center edit table-text">Edit</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td class="task-list-item mt-1 pointer noselect">
            <span
              @click="changeStatus(index)"
              style="color: white"
              :class="{ 'line-through': task.status === 'complete' }"
            >
              {{ task.name }}
            </span>
          </td>
          <td>
            <span
              class="pointer noselect"
              @click="changeStatus(index)"
              :class="{
                'text-danger': task.status === 'incomplete',
                'text-success': task.status === 'complete',
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
    <div class="extra-container" id="statusFilter">
      <label>Filter</label>
      <select v-model="status2">
        <option value="">All</option>
        <option value="incomplete">Incompleted</option>
        <option value="complete">Completed</option>
      </select>
    </div>
  </div>
</template>

<style scoped>
.btn-sub {
  background-color: #198754;
}

.fa-trash {
  color: #c70039;
}

.main-title {
  font-family: Montserrat, sans-serif;
  font-weight: 800;
  color: antiquewhite;
}

.table-bordered > :not(caption) > * {
  border-width: 0px;
}

.table-bordered > :not(caption) > * > * {
  border-width: 0px;
}

select {
  margin-left: 15px;
  width: 150px;
  padding: 5px 35px 5px 5px;
  font-size: 16px;
  border: 1px solid #ccc;
  height: 34px;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  background: url(https://freepngimg.com/download/xbox/41285-1-game-controller-png-download-free.png)
    96% / 15% no-repeat #eee;
}

.table-text {
  color: antiquewhite;
  font-family: Montserrat, sans-serif;
  font-weight: 800;
}

.add-task {
  display: flex;
}

.form-control {
  border: none;
  outline: none;
  border-bottom: 1px solid #272727;
  background-color: transparent;
  margin-right: 15px;
  color: #272727;
  box-shadow: none;
  border-radius: 0;
}

.form-control::placeholder {
  color: #272727;
}

.task-list-item {
  background-color: rgba(255, 255, 255, 0.3);
  display: flex;
  align-items: center;
  padding: 8px;
  border-radius: 35px;
  margin-bottom: 12px;
}

.pointer {
  cursor: pointer;
}
.noselect {
  -webkit-touch-callout: none; /* iOS Safari */
  -webkit-user-select: none; /* Safari */
  -khtml-user-select: none; /* Konqueror HTML */
  -moz-user-select: none; /* Old versions of Firefox */
  -ms-user-select: none; /* Internet Explorer/Edge */
  user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome, Edge, Opera and Firefox */
}
.line-through {
  text-decoration: line-through;
}

svg {
  color: turquoise;
}

span {
  font-family: Montserrat, sans-serif;
  font-weight: 600;
}
.container {
  width: 100%;
  max-height: 100%;
  background-color: rgba(255, 255, 255, 0.3);
  padding: 25px;
  border-radius: 25px;
  overflow: auto;
  color: #222;
}
</style>
