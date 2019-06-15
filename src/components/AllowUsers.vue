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
                        <span class="user">{{ user.email }} ( {{user.rol}} )</span>
                    </div>
                    <div>
                        <button class="btn btn-danger" @click="deleteUser(user)" type="submit">Desactivar Usuario</button>
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
    deleteUser: function (user) {
      const promises = []
      firebase.firestore().collection('Users').where('email', '==', user.email).get().then(query => {
        query.forEach(doc => {
          promises.push(
            doc.ref.update({
              active: false
            })
          )
          return Promise.all(promises)
        })
      }).then(() => {
        firebase.firestore().collection('AllowedUsers').where('email', '==', user.email).get().then(query => {
          query.forEach(doc => {
            doc.ref.delete().then(() => {
              this.loadUsers()
            }, error => {
              if (error) {
                console.log(error.meesage)
              }
            })
          })
        })
      }, error => {
        if (error) {
          console.log(error.message)
        }
      })
    },
    loadUsers: function () {
      this.users = []
      firebase.firestore().collection('AllowedUsers').get().then(query => {
        if (query.size > 0) {
          query.forEach(doc => {
            let data = {
              email: doc.data().email,
              rol: doc.data().rol === 'Administrator' ? 'Administrador' : 'Empleado'
            }
            this.users.push(data)
          })
        }
      })
    },
    saveUser: function () {
      if (this.isEmpty(this.email) || !this.validSelect(this.rol)) {
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
</style>
