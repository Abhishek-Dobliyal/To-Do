<template>
  <div class="jumbotron vertical-center">
      <div class="container text-center">
          <span class="lead display-3">To-Do <i class="fa fa-file-text" style="color: royalblue;"></i></span>
          <br>
          <span class="lead p-3">Get it done now or never...</span>
          <div class="row pt-4">
              <div class="col-sm-12">
                  <button type="button" class="btn btn-success mt-2" data-toggle="modal" data-target="#formModal">Add Task</button>
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
                                        <input type="checkbox" v-model="formData.isCompleted" class="form-check-input" id="played">
                                        <label class="form-check-label" for="played">Is this task completed?</label>
                                    </div>
                                </form>
                            </div>
                            <div class="modal-footer">
                                <button type="button" class="btn btn-danger" data-dismiss="modal">Cancel</button>
                                <button type="button" class="btn btn-success" @click="submitForm" data-dismiss="modal">Add Task</button>
                            </div>
                        </div>
                    </div>
                  </div>
                  <br>
                  <div class="container d-flex justify-content-center">
                    <table class="table table-hover mt-4">
                        <thead>
                            <tr>
                                <th scope="col">Title</th>
                                <th scope="col">Task Description</th>
                                <th scope="col">Task Completed</th>
                                <th scope="col">Actions</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="item, idx in data" :key="idx">
                                <td>{{item.title}}</td>
                                <td>{{item.desc}}</td>
                                <td>{{item.completed ? 'YES' : 'NO'}}</td>
                                <td>
                                    <div class="btn-group" role="group">
                                        <button class="btn btn-warning btn-sm" data-toggle="modal" data-target="#updateTaskModal" @click="saveTaskId(item.id)">Update</button>
                                        <button class="btn btn-danger btn-sm" @click="deleteTask(item.id)">Delete</button>
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
                                        <input type="checkbox" v-model="updatedData.isCompleted" class="form-check-input" id="played">
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
            data: Array(),
            apiUrl: 'http://localhost:5000',
            formData: {
                title: '',
                desc: '',
                isCompleted: false
            },
            updatedData: {
                title: '',
                desc: '',
                isCompleted: false,
                id: 0
            }
        }
    },
    methods: {
        fetchTasks() {
            axios.get(this.apiUrl)
            .then((res) => {
                this.data = res.data;
                console.log(this.data);
            })
            .catch((err) => {
                console.log(err);
            })
        },

        submitForm() {
            axios.post(this.apiUrl, this.formData)
            .then((res) => {
                console.log(res);
            }).catch((err) => {
                console.log(err);
            })
        }, 

        deleteTask(id) {
            const deleteUrl = 'http://localhost:5000/delete';
            axios.post(deleteUrl, {'id': id})
            .then((res) => {
                console.log(res);
            })
        },

        saveTaskId(id) {
            this.updatedData.id = id;
        },

        updateTask() {
            const updateUrl = 'http://localhost:5000/update';
            axios.post(updateUrl, this.updatedData)
            .then((res) => {
                console.log(res);
            }).catch((err) => {
                console.log(err);
            })
        }
    },
    created() {
        this.fetchTasks();
    },

    updated() {
        this.fetchTasks();
    }
}
</script>
