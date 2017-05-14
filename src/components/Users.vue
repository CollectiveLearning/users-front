<template id="users">
  <div>
    <div class="form" v-if="onCreate || onEdit">
      <h3 v-if="onCreate">New User</h3>
      <h3 v-if="onEdit">Edit User</h3>
      <div class="row">
        <div class="col-md-6">
          <label for="name">Name</label>
          <input type="text" name="name" v-model="user.name" class="form-control">
          <span class="text-danger">{{errors.name}}</span>
        </div>
        <div class="col-md-6">
          <label for="username">Username</label>
          <input type="text" name="username" v-model="user.username" class="form-control">
          <span class="text-danger">{{errors.username}}</span>
        </div>

        <div class="col-md-6">
          <label for="email">Email</label>
          <input type="text" name="email" v-model="user.email" class="form-control">
          <span class="text-danger">{{errors.email}}</span>
        </div>

        <div class="col-md-6">
          <label for="rol">Role</label>
          <select class="form-control" name="role" v-model="user.role">
            <option value="">[ Select role... ]</option>
            <option v-for="rol in roles" v-bind:value="rol">{{rol}}</option>
          </select>
          <span class="text-danger">{{errors.role}}</span>
        </div>

        <div class="col-md-6">
          <label for="password">Password</label>
          <input type="text" name="password" v-model="user.password" class="form-control">
          <span class="text-danger">{{errors.password}}</span>
        </div>
      </div>

      <div class="col-md-6">
        <a href="#" @click="create" v-if="onCreate" class="btn btn-success">Create</a>
        <a href="#" @click="update" v-if="onEdit" class="btn btn-success">Edit</a>
      </div>
    </div>

    <div class="list" v-if="!onCreate && !onEdit">
      <div class="row">
        <div class="col-md-11">
          <h3>Users</h3>
        </div>
        <div class="col-md-1">
          <a href="#" @click="fetchAll">Refresh</a>
        </div>
      </div>
      <table class="table table-sm">
        <tr>
          <th>ID</th>
          <th>Name</th>
          <th>Username</th>
          <th>Email</th>
          <th>Role</th>
          <th>Status</th>
          <th>Actions</th>
        </tr>
        <tr v-for="(user, index) in users">
          <td>{{user.id}}</td>
          <td>{{user.name}}</td>
          <td>{{user.username}}</td>
          <td>{{user.email}}</td>
          <td>{{user.role}}</td>
          <td>{{user.status}}</td>
          <td>
            <a href="#" @click="edit(index)">Edit</a>
            <a href="#" @click="remove(user.id)">Delete</a>
          </td>
        </tr>
      </table>

      <a href="#" class="btn btn-primary" @click="onCreate = true">Add user</a>
    </div>

  </div>
</template>

<script>
  export default {
    name: 'Users',
    data () {
      return {
        onCreate: false,
        onEdit: false,
        user: { name: '', username: '', email: '', role: 'customer', password: '' },
        users: [],
        errors: {},
        roles: ['customer', 'support', 'admin']
      }
    },
    methods: {
      fetchAll () {
        this.$http.get('users.json')
        .then(data => {
          this.users = data.body
        })
      },
      remove (id) {
        this.$http.delete(`users/${id}.json`)
          .then(() => this.fetchAll())
      },
      create () {
        this.$http.post('users', this.user)
          .then(response => {
            this.fetchAll()
            this.onCreate = false
            this.user = { name: '', username: '', email: '', role: 'customer', password: '' }
          }, response => {
            this.errors = response.body
          })
      },
      edit (index) {
        this.onEdit = true
        this.user = this.users[index]
      },
      update () {
        this.$http.patch(`users/${this.user.id}`, this.user)
          .then(response => {
            this.fetchAll()
            this.onEdit = false
            this.user = { name: '', username: '', email: '', role: 'customer', password: '' }
          }, response => {
            this.errors = response.body
          })
      }
    },
    mounted () {
      this.fetchAll()
    }
  }
</script>
