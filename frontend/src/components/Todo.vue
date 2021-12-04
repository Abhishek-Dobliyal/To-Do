<template>
  <div class="jumbotron vertical-center">
    <div class="container text-center">
      <span class="lead display-3">To-Do <i class="fa fa-file-text" style="color: royalblue;"></i></span>
      <br>
      <span class="lead p-3">Get it done now or never...</span>
      <div class="row pt-4">
        <div class="col-sm-12">
          <button type="button" class="btn btn-success mt-2" data-toggle="modal" data-target="#formModal">Add New Task
          </button>
          <div class="modal fade" id="formModal" tabindex="-1" role="dialog">
            <div class="modal-dialog" role="document">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title" id="exampleModalLabel">Add a new task</h5>
                  <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                  </button>
                </div>
                <div class="modal-body">
                  <form>
                    <div class="mb-3">
                      <label for="title" class="form-label">Title</label>
                      <input type="text" v-model="formData.title" class="form-control" id="title" placeholder="Give a title here...">
                    </div>
                    <div class="form-floating">
                      <textarea class="form-control" v-model="formData.desc" id="floatingTextarea" style="height: 100px"></textarea>
                      <label for="floatingTextarea">Add description</label>
                    </div>
                    <div class="mt-2 form-check">
                      <input type="checkbox" v-model="formData.completed" class="form-check-input" id="played">
                      <label class="form-check-label" for="played">Is this task completed?</label>
                    </div>
                  </form>
                </div>
                <div class="modal-footer">
                  <button type="button" class="btn btn-danger" data-dismiss="modal">Cancel</button>
                  <button type="button" class="btn btn-success" @click="submitForm" data-dismiss="modal">Add Task
                  </button>
                </div>
              </div>
            </div>
          </div>
          <br>
          <div class="container d-flex justify-content-center">
            <table class="table table-hover mt-4">
              <thead>
              <tr>
                <th scope="col">Completed</th>
                <th scope="col">Title</th>
                <th scope="col">Task Description</th>
                <th scope="col">Actions</th>
              </tr>
              </thead>
              <tbody>
              <tr v-for="(item, idx) in data" :key="idx">
                <td><input type="checkbox" id="item.title" v-model="item.completed" @click="completeTask(idx)"></td>
                <td>{{ item.title }}</td>
                <td>{{ item.desc }}</td>
                <td>
                  <div class="btn-group" role="group">
                    <button class="btn btn-warning btn-sm" data-toggle="modal" data-target="#updateTaskModal"
                            @click="saveTaskId(item.id)">Update
                    </button>
                    <button class="btn btn-danger btn-sm" @click="deleteTask(item.id, idx)">Delete</button>
                  </div>
                </td>
              </tr>
              </tbody>
            </table>
            <div class="modal fade" id="updateTaskModal" tabindex="-1" role="dialog">
              <div class="modal-dialog" role="document">
                <div class="modal-content">
                  <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">Update an existing task</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                      <span aria-hidden="true">&times;</span>
                    </button>
                  </div>
                  <div class="modal-body">
                    <form>
                      <div class="mb-3">
                        <label for="title" class="form-label">Update Title</label>
                        <input type="text" v-model="updatedData.title" class="form-control" id="title" placeholder="New title here...">
                      </div>
                      <div class="form-floating">
                        <textarea class="form-control" v-model="updatedData.desc" id="floatingTextarea" style="height: 100px"></textarea>
                        <label for="floatingTextarea">Update description</label>
                      </div>
                      <div class="mt-2 form-check">
                        <input type="checkbox" v-model="updatedData.completed" class="form-check-input" id="played">
                        <label class="form-check-label" for="played">Is this task completed?</label>
                      </div>
                    </form>
                  </div>
                  <div class="modal-footer">
                    <button type="button" class="btn btn-danger" data-dismiss="modal">Cancel</button>
                    <button type="button" class="btn btn-success" @click="updateTask" data-dismiss="modal">Update Task</button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Todo',
  data() {
    return {
      data: [],
      apiUrl: 'http://localhost:5000',
      formData: {
        title: '',
        desc: '',
        completed: false
      },
      updatedData: {
        title: '',
        desc: '',
        completed: false,
        id: 0
      }
    }
  },
  methods: {
    async fetchTasks() {
      axios.get(this.apiUrl)
          .then((res) => {
            this.data = res.data;
            console.log(this.data);
          })
          .catch((err) => {
            console.log(err);
          })
    },

    async submitForm() {
      axios.post(this.apiUrl, this.formData)
          .then((res) => {
            this.data = res.data;
            this.formData = {
              title: '',
              desc: '',
              completed: false
            }
          }).catch((err) => {
        console.log(err);
      })
    },

    async completeTask(tID) {
      axios.post(this.apiUrl + "/complete", {"id": this.data[tID].id})
        .then( () => {
        }).catch((err) => console.log(err));
    },

    async deleteTask(id, idx) {
      axios.post(this.apiUrl + "/delete", {'id': id})
          .then(() => {
            this.data.splice(idx, 1)
            console.log(this.data)
          })
    },

    saveTaskId(id) {
      this.updatedData = JSON.parse(JSON.stringify(this.data.filter((el) => el.id === id)[0]))
      console.log(this.data)
    },

    updateTask() {
      axios.post(this.apiUrl + '/update', this.updatedData)
          .then(() => {
            let dados = this.data.filter((el) => el.id === this.updatedData.id)[0]
            dados.id = this.updatedData.id
            dados.title = this.updatedData.title
            dados.desc = this.updatedData.desc
            dados.completed = this.updatedData.completed
            console.log(this.data.filter((el) => el.id === this.updatedData.id)[0])
            console.log(dados)
            console.log(this.updatedData)
          }).catch((err) => {
        console.log(err);
      })
    }
  },

  async created() {
    await this.fetchTasks();
  },

  restartObj() {
    return {
      title: '',
      desc: '',
      completed: false,
      id: 0
    }
  }
}
</script>
