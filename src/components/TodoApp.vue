<template>
  <div class="container" style="max-width:800px">
    <!--headings -->

    <h3 class="text-center">To Do App using VueJs</h3>

    <!--Form-->
    <div class="d-flex mt-5 ">
      <input
        type="text"
        v-model="task"
        placeholder="Enter new task in the list"
        class="w-100 form-control"
      />
    </div>

    <div class=" center">
      <br />
      <button class="btn btn-success b1 pointer" @click="submitTask">
        <span v-if="editedTask == null">Add</span>
        <span v-if="editedTask != null">Update</span>
        </button>
      <button class="btn btn-primary b1 pointer" @click="cleatTask">Clear</button>
      <button v-if="deletedTask.length > 0" class="btn btn-danger b1 pointer" @click="deletedTaskModal = true">Deleted Task(s)</button>
    </div>

    <!-- Task table -->
    <table class="table table-bordered mt-3 bg ">
      <thead>
        <tr>
          <th scope="col">Task</th>
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
            <span
              class="pointer noselect"
              @click="changeStatus(index)"
              :class="{
                'text-danger': task.status === 'to-do',
                'text-success': task.status === 'finished',
                'text-warning': task.status === 'in-progress',
              }"
            >
              {{ capitalizeFirstChar(task.status) }}
            </span>
          </td>
          <td class="text-center">
            <div v-if="editedTask != index" @click="deleteTask(index)">
              <span class="fa fa-trash pointer"></span>
            </div>
          </td>
          <td class="text-center">
            <div v-if="editedTask != index" @click="editTask(index)">
              <p class="fa fa-pen pointer"></p>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

      <div v-if="deletedTaskModal">
        <transition name="modal">
          <div class="modal-mask">
            <div class="modal-wrapper">
              <div class="modal-dialog">
                <div class="modal-content">
                  <div class="modal-header">
                    <h4 class="modal-title" >Deleted Task(s)</h4>
                    <button type="button" class="close" aria-hidden="true" @click="deletedTaskModal=false">&times;</button>
                  </div>
                  <div class="modal-body">
                    <table>
                      <tr v-for="(task, index) in deletedTask" :key="index">
                        <td style="width: 300px;">{{task.name}}</td>
                        <td style="width: 100px;">
                          <span :class="{
                                        'text-danger': task.status === 'to-do',
                                        'text-success': task.status === 'finished',
                                        'text-warning': task.status === 'in-progress',
                                      }">
                                      {{ capitalizeFirstChar(task.status) }}
                            </span>
                        </td>
                        <td><button style="width: 60px" class="btn btn-primary" @click="undoTask(index)">undo</button></td>
                      </tr>
                    </table>                
                  </div>
                </div>
              </div>
            </div>
          </div>
        </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },

  data() {
    return {
      task: "",
      editedTask: null,
      statuses: ["to-do", "in-progress", "finished"],
      /* Status could be: 'to-do' / 'in-progress' / 'finished' */
      tasks: [
        {
          name: "Create a MongoDb server with Docker",
          status: "to-do",
        },
        {
          name: "Read a Book",
          status: "in-progress",
        },
        {
          name: "Learn from a tutorial",
          status: "finished",
        },
      ],
      deletedTask: [],
      deletedTaskModal:false
    };
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
      let deletedTask = this.tasks[index];
      this.deletedTask.push(deletedTask);
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
      /* We need to update the task */
      if (this.editedTask != null) {
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      } else {
        /* We need to add new task */
        this.tasks.push({
          name: this.task,
          status: "to-do",
        });
      }
      this.task = "";
    },
    // undo Deleted Task
    undoTask(index){
      this.tasks.push(this.deletedTask[index]);
      this.deletedTask.splice(index, 1);
      if(this.deletedTask.length <= 0)
        this.deletedTaskModal = false;
    },
    // cleatTask
    cleatTask(){
      this.task="";
      this.editedTask=null;
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.pointer {
  cursor: pointer;
}
.line-through {
  text-decoration: line-through;
}
.b1 {
  padding: 10px;
  /* float: right; */
  margin: 5px;
}
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity 0.5s ease;
}
.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

</style>
