<template>
    <div class="background">
        <Navbar :admin="admin"/>
        <div class="container p-5">
            <h3 class="my-2">{{ user }}</h3>
            <hr />
            <div class="alert alert-danger text-center" v-if="error">
                <span>{{ message }}</span>
            </div>
            <div class="alert alert-success text-center" v-if="success">
                <span>{{ message }}</span>
            </div>
            <form action="" class="mt-5 mb-5 w-75 container">
                <div v-if="!active">
                  <div class="mt-3 mb-3">
                      <label for="inputName" class="text-left h5 mt-1 mb-1">Nombre:</label>
                      <input type="text" class="form-control mt-1 mb-1" id="inputName" v-model="name">
                  </div>
                  <div class="mt-3 mb-3">
                      <label for="inputFirstLastname" class="text-left h5 mt-1 mb-1">Primer apellido:</label>
                      <input type="text" class="form-control mt-1 mb-1" id="inputFirstLastname" v-model="firstLastname">
                  </div>
                  <div class="mt-3 mb-3">
                      <label for="inputSecondLastname" class="text-left h5 mt-1 mb-1">Segundo apellido:</label>
                      <input type="text" class="form-control" id="inputSecondLastname mt-1 mb-1" v-model="secondLastname">
                  </div>
                  <div class="mt-4 mb-4">
                      <button class="btn btn-save font-weight-bold" @click.prevent="update">Guardar</button>
                      <button class="btn btn-password ml-3 font-weight-bold" @click.prevent="activeForm">Cambiar Contraseña</button>
                  </div>
                </div>
                <div v-else>
                  <div class="mt-3 mb-3">
                      <label for="inputOldPassword" class="text-left h5 mt-1 mb-1">Antigua contraseña:</label>
                      <input type="password" class="form-control" id="inputOldPassword" v-model="oldPassword">
                  </div>
                  <div class="mt-3 mb-3">
                      <label for="inputNewPassword" class="text-left h5 mt-1 mb-1">Nueva contraseña:</label>
                      <input type="password" class="form-control" id="inputNewPassword" v-model="newPassword">
                  </div>
                  <div class="mt-3 mb-3">
                      <label for="inputConfirmPassword" class="text-left h5 mt-1 mb-1">Confirmar contraseña:</label>
                      <input type="password" class="form-control" id="inputConfirmPassword" v-model="confirmPassword">
                  </div>
                  <div class="mt-4 mb-4">
                      <button class="btn btn-save font-weight-bold" @click.prevent="updatePassword">Guardar</button>
                      <button class="btn btn-danger font-weight-bold ml-3" @click="activeForm">Cancelar</button>
                  </div>
                </div>
            </form>
        </div>
    </div>
</template>

<script>
import firebase from 'firebase'
import Navbar from './Navbar'

export default {
  name: 'profile',
  data: function () {
    return {
      admin: '',
      name: '',
      firstLastname: '',
      secondLastname: '',
      user: '',
      active: false,
      error: false,
      message: '',
      oldPassword: '',
      newPassword: '',
      confirmPassword: '',
      success: false
    }
  },
  created () {
    this.isAdmin()
    this.load()
  },
  methods: {
    update: function () {
      if (this.isEmpty(this.name) || this.isEmpty(this.firstLastname) || this.isEmpty(this.secondLastname)) {
        this.message = 'Campos obligatorios'
        this.error = true
      } else {
        const promises = []
        firebase.firestore().collection('Users').where('email', '==', firebase.auth().currentUser.email).get().then(query => {
          query.forEach(doc => {
            promises.push(
              doc.ref.update({
                name: this.name,
                first_lastname: this.firstLastname,
                second_lastname: this.secondLastname
              })
            )
            this.error = false
            return Promise.all(promises)
          })
        }).then(() => {
          this.isAdmin()
          this.load()
        }, error => {
          console.log(error.message)
        })
      }
    },
    load: function () {
      firebase.firestore().collection('Users').where('email', '==', firebase.auth().currentUser.email).get().then(query => {
        if (query.size > 0) {
          this.user = query.docs[0].data().name + ' ' + query.docs[0].data().first_lastname + ' ' + query.docs[0].data().second_lastname
          this.name = query.docs[0].data().name
          this.firstLastname = query.docs[0].data().first_lastname
          this.secondLastname = query.docs[0].data().second_lastname
        }
      })
    },
    isAdmin: function () {
      firebase.firestore().collection('AllowedUsers').where('email', '==', firebase.auth().currentUser.email).get().then(query => {
        if (query.size > 0) {
          this.admin = query.docs[0].data().rol === 'Administrator'
        }
      })
    },
    activeForm: function () {
      this.active = !this.active
      this.error = false
      this.success = false
    },
    updatePassword: function () {
      if (this.isEmpty(this.oldPassword) || this.isEmpty(this.newPassword) || this.isEmpty(this.confirmPassword)) {
        this.message = 'Campos obligatorios'
        this.error = true
      } else {
        if (this.newPassword === this.confirmPassword) {
          let user = firebase.auth().currentUser
          let credential = firebase.auth.EmailAuthProvider.credential(user.email, this.oldPassword)
          user.reauthenticateWithCredential(credential).then(() => {
            user.updatePassword(this.newPassword).then(() => {
              this.message = 'Contraseña modificada con éxito'
              this.success = true
              this.error = false
            }, error => {
              if (error) {
                this.message = 'Error al modificar la contraseña'
                this.error = true
              }
            })
          }, error => {
            if (error) {
              console.log(error.message)
            }
          })
        } else {
          this.message = 'Las contraseñas no coinciden'
          this.error = true
        }
      }
    },
    isEmpty: function (value) {
      return value === ''
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

  .btn-save{
    background-color: #3d3d3d;
    color: #c6dd6b;
  }

  .btn-password{
    background-color: #c6dd6b;
    color: #3d3d3d;
  }
</style>
