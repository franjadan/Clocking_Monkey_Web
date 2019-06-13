<template>
    <div class="container centered">
      <div class="register shadow rounded w-75">
        <div class="row">
          <div class="col-md-4 register-left">
            <img src="../../media/clockingmonkey_logo.png" alt="Logo Clocking Monkey" class="w-75">
            <h2 class="nameApp">Clocking Monkey</h2>
          </div>
          <div class="col-md-8 register-right">
            <div class="center">
              <h3 class="title_login">Bienvenido</h3>
              <h6>Regístrate y accede a tu cuenta</h6>
            </div>
            <form action="index.html" class="p-5">
              <div class="m-2">
                <label for="inputName" class="text-left h6">Nombre:</label>
                <input type="text" id="inputName" class="form-control" v-model="name">
              </div>
              <div class="m-2">
                <label for="inputFirstLastname" class="text-left h6">Primer apellido:</label>
                <input type="text" id="inputFirstLastname" class="form-control" v-model="firstLastname">
              </div>
              <div class="m-2">
                <label for="inputSecondLastname" class="text-left h6">Segundo apellido:</label>
                <input type="text" id="inputSecondLasname" class="form-control" v-model="secondLastname">
              </div>
              <div class="m-2">
                <label for="inputEmail" class="text-left h6">Email:</label>
                <input type="email" id="inputEmail" class="form-control" v-model="email">
              </div>
              <div class="m-2">
                <label for="inputPassword" class="text-left h6">Contraseña:</label>
                <input type="password" id="inputPassword" class="form-control" v-model="password">
              </div>
              <div class="alert alert-danger mt-3 text-center" v-if="error">
                <span class="error">{{ message }}</span>
              </div>
              <div class="form-group mx-2 mt-5">
                <button class="btn btn-block p-2" @click.prevent="register">Registrar</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
</template>

<script>
import firebase from 'firebase'

export default {
  name: 'register',
  data: function () {
    return {
      name: '',
      firstLastname: '',
      secondLastname: '',
      email: '',
      password: '',
      message: '',
      error: false
    }
  },
  methods: {
    register: function () {
      firebase.firestore().collection('AllowedUsers').where('email', '==', this.email).get().then(query => {
        if (query.size > 0) {
          if (this.isEmpty(this.name) || this.isEmpty(this.email) || this.isEmpty(this.firstLastname) || this.isEmpty(this.secondLastname) || this.isEmpty(this.password)) {
            this.message = 'Campos obligatorios'
            this.error = true
          } else {
            firebase.auth().createUserWithEmailAndPassword(this.email, this.password).then(user => {
              let data = {
                email: this.email,
                first_lastname: this.firstLastname,
                second_lastname: this.secondLastname,
                name: this.name
              }
              firebase.firestore().collection('Users').add(data).then(result => {
                this.$router.push({ path: '/' })
              }).catch(error => {
                if (error) {
                  this.message = 'Error al realizar el registro'
                  this.error = true
                }
              })
            })
          }
        } else {
          this.message = 'Esta dirección de correo electrónico no puede ser registrada'
          this.error = true
        }
      })
    },
    isEmpty: function (value) {
      return value === ''
    }
  }
}
</script>

<style scoped>
  @font-face { font-family: computer_pixel-7; src: url('../../fonts/computer_pixel-7.ttf'); }

    h1,h2,h3,h4{
        color: #000000;
    }

    .container{
        margin: 0 auto;
        max-width: 100%;
        width: 1400px;
        font-size: 12px !important;
    }

    .centered{
        display: flex;
        flex-direction: row;
        justify-content: center;
        min-height: 100vh;
        align-items: center;
    }

    .register{
        background: rgba(104,159,56,0.5);
        margin-top: 3%;
        padding: 3%;
    }

    .nameApp{
        font-family: computer_pixel-7;
        font-size: 3.5em;
        color: #ffffff;
    }

    .register-left{
        text-align: center;
        color: #000000;
        margin-top: 4%;
    }

    .register-right{
        background-color: rgb(255,255,255);
        border-top-left-radius: 10% 50%;
        border-bottom-left-radius: 10% 50%;
    }

    .center{
        justify-content: center;
        align-items: center;
        text-align: center;
    }

    .title_login{
        margin-top: 1.5em;
    }

    .btn{
        background-color: rgba(104,159,56);
        color: #ffffff;
    }

    .error{
      font-size: 1.25em;
    }
</style>
