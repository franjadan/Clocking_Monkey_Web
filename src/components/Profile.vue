<template>
    <div>
        <Navbar :admin="admin"/>
        <div class="container mt-5">
            <h5 class="my-2">{{ user }}</h5>
            <hr />
            <div class="alert alert-danger text-center" v-if="error">
                <span>{{ message }}</span>
            </div>
            <div class="alert alert-success text-center" v-if="success">
                <span>{{ message }}</span>
            </div>
            <form action="" class="my-4 w-75 container">
                <div v-if="!active">
                  <div class="m-2">
                      <label for="inputName" class="text-left h5">Nombre:</label>
                      <input type="text" class="form-control" id="inputName" v-model="name">
                  </div>
                  <div class="m-2">
                      <label for="inputFirstLastname" class="text-left h5">Primer apellido:</label>
                      <input type="text" class="form-control" id="inputFirstLastname" v-model="firstLastname">
                  </div>
                  <div class="m-2">
                      <label for="inputSecondLastname" class="text-left h5">Segundo apellido:</label>
                      <input type="text" class="form-control" id="inputSecondLastname" v-model="secondLastname">
                  </div>
                  <div class="mt-3">
                      <button class="btn btn-save" @click.prevent="update">Guardar</button>
                      <button class="btn btn-save ml-2" @click.prevent="activeForm">Cambiar Contraseña</button>
                  </div>
                </div>
                <div v-else>
                  <div class="m-2">
                      <label for="inputOldPassword" class="text-left h5">Antigua contraseña:</label>
                      <input type="password" class="form-control" id="inputOldPassword" v-model="oldPassword">
                  </div>
                  <div class="m-2">
                      <label for="inputNewPassword" class="text-left h5">Nueva contraseña:</label>
                      <input type="password" class="form-control" id="inputNewPassword" v-model="newPassword">
                  </div>
                  <div class="m-2">
                      <label for="inputConfirmPassword" class="text-left h5">Confirmar contraseña:</label>
                      <input type="password" class="form-control" id="inputConfirmPassword" v-model="confirmPassword">
                  </div>
                  <div class="mt-3">
                      <button class="btn btn-save" @click.prevent="updatePassword">Guardar</button>
                      <button class="btn btn-danger" @click="activeForm">Cancelar</button>
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
    .btn-save{
        background-color: rgba(104,159,56);
        color: #ffffff;
    }
</style>
