<template>
    <div class="background">
        <Navbar :admin="true"/>
        <div class="container p-5">
            <button class="btn mb-3 mt-3 py-2 px-5 font-weight-bold btn-add" v-if="!active" @click.prevent="activeForm"><i class="fas fa-user-plus mr-1"></i> Añadir Usuario</button>
            <form action="" class="mt-5 mb-5" v-if="active">
                <h3>Añadir usuario permitido</h3>
                <div class="alert alert-danger text-center my-2" v-if="error">
                  <span class="error">{{ message }}</span>
                </div>
                <div class="row">
                    <div class="m-2 col">
                        <label for="inputEmail" class="text-left h6">Email:</label>
                        <input type="email" name="" id="inputEmail" class="form-control" v-model="email">
                    </div>
                    <div class="m-2 col">
                        <label for="selectRol" class="text-left h6">Rol:</label>
                        <select name="" id="selectRol" class="form-control" v-model="rol">
                            <option value="" disabled>Selecione un rol</option>
                            <option value="Administrator">Administrador</option>
                            <option value="Employee">Empleado</option>
                        </select>
                    </div>
                </div>
                <div class="mt-3 mb-3">
                  <button class="btn btn-save font-weight-bold mr-3" @click="saveUser">Guardar</button>
                  <button class="btn btn-danger font-weight-bold" @click="activeForm">Cancelar</button>
                </div>
            </form>
            <ul class="list-group">
                <li v-for="user in users" v-bind:key="user" class="list-group-item d-flex justify-content-between align-items-center">
                    <div>
                        <p class="user">{{ user.name }}</p>
                        <p class="user">{{ user.email }} ({{ user.rol }})</p>
                    </div>
                    <div class="mr-2">
                        <button  v-if="user.active" class="btn btn-custom btn-danger font-weight-bold" @click="activeUser(user, false)" type="submit">Desactivar</button>
                        <button  v-else class="btn btn-custom btn-success font-weight-bold" @click="activeUser(user, true)" type="submit">Activar</button>
                    </div>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import firebase from 'firebase'
import Navbar from './Navbar'

export default {
  name: 'allowUsers',
  data: function () {
    return {
      users: [],
      email: '',
      rol: '',
      active: false,
      error: false,
      message: ''
    }
  },
  created () {
    this.loadUsers()
  },
  methods: {
    activeUser: function (user, active) {
      const promises = []
      firebase.firestore().collection('Users').where('email', '==', user.email).get().then(query => {
        query.forEach(doc => {
          promises.push(
            doc.ref.update({
              active: active
            })
          )
          return Promise.all(promises)
        })
      }).then(() => {
        this.loadUsers()
      })
    },
    loadUsers: function () {
      this.users = []
      firebase.firestore().collection('AllowedUsers').get().then(query => {
        query.forEach(doc => {
          let email = doc.data().email
          let rol = doc.data().rol === 'Administrator' ? 'Administrador' : 'Empleado'
          firebase.firestore().collection('Users').where('email', '==', email).get().then(query => {
            if (query.size > 0) {
              let name = query.docs[0].data().name + ' ' + query.docs[0].data().first_lastname + ' ' + query.docs[0].data().second_lastname
              let active = query.docs[0].data().active
              let data = {
                name: name,
                email: email,
                active: active,
                rol: rol
              }
              this.users.push(data)
            } else {
              let data = {
                name: ' ',
                email: email,
                active: ' ',
                rol: rol
              }
              this.users.push(data)
            }
          })
        })
      })
    },
    saveUser: function () {
      if (this.isEmpty(this.email) || this.validSelect(this.rol)) {
        this.message = 'Campos obligatorios'
        this.error = true
      } else {
        let data = {
          email: this.email,
          rol: this.rol
        }
        firebase.firestore().collection('AllowedUsers').add(data).then(result => {
          this.loadUsers()
          this.email = ''
          this.active = !this.active
        }, error => {
          if (error) {
            console.log(error.meesage)
          }
        })
      }
    },
    activeForm: function () {
      this.active = !this.active
      this.error = false
    },
    isEmpty: function (value) {
      return value === ''
    },
    validSelect: function (value) {
      return this.rol === 'Selecione un rol'
    }
  },
  components: {
    Navbar
  }
}
</script>

<style scoped>
  h1,h2,h3,h4,h5,h6{
    color: #282828;
  }

  .h1,.h2,.h3,.h4,.h5,.h6{
    color: #282828;
  }

  .background{
    background-color: #fcfff8;
  }

  .btn-add{
    background-color: #c6dd6b;
    color: #3d3d3d;
  }

  .user{
    color: #282828;
  }

  .btn-save{
    background-color: #3d3d3d;
    color: #c6dd6b;
  }

  .btn-custom{
    width: 8em;
  }
</style>
