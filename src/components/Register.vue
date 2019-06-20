<template>
    <div class="background">
      <div class="container centered">
        <div class="register shadow-lg w-75 mb-5">
          <div class="row d-flex align-items-center">
            <div class="col-md-4 register-left">
              <img src="../../media/clockingmonkey_logo.png" alt="Logo Clocking Monkey" class="w-75 mt-5">
              <h2 class="nameApp">Clocking Monkey</h2>
            </div>
            <div class="col-md-8 register-right">
              <div class="center mt-3 mb-3">
                <h2 class="title_login mt-1 mb-1">Bienvenido</h2>
                <h4 class="mt-1 mb-1">Regístrate y accede a tu cuenta</h4>
              </div>
              <form action="index.html" class="col-md-12 my-4">
                <div class="mt-3 mb-3 mx-5">
                  <label for="inputName" class="text-left h6">Nombre:</label>
                  <input type="text" id="inputName" class="form-control" v-model="name">
                </div>
                <div class="mt-3 mb-3 mx-5">
                  <label for="inputFirstLastname" class="text-left h6">Primer apellido:</label>
                  <input type="text" id="inputFirstLastname" class="form-control" v-model="firstLastname">
                </div>
                <div class="mt-3 mb-3 mx-5">
                  <label for="inputSecondLastname" class="text-left h6">Segundo apellido:</label>
                  <input type="text" id="inputSecondLasname" class="form-control" v-model="secondLastname">
                </div>
                <div class="mt-3 mb-3 mx-5">
                  <label for="inputEmail" class="text-left h6">Email:</label>
                  <input type="email" id="inputEmail" class="form-control" v-model="email">
                </div>
                <div class="mt-3 mb-3 mx-5">
                  <label for="inputPassword" class="text-left h6">Contraseña:</label>
                  <input type="password" id="inputPassword" class="form-control" v-model="password">
                </div>
                <div class="alert alert-danger mt-3 text-center" v-if="error">
                  <span class="error">{{ message }}</span>
                </div>
                <div class="form-group mx-5 mt-4 mb-4">
                  <button class="btn btn-block p-2 font-weight-bold btn-register" @click.prevent="register">Registrar</button>
                </div>
              </form>
            </div>
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
                name: this.name,
                active: true
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

  h1,h2,h3,h4,h5,h6{
    color: #282828;
  }

  .h1,.h2,.h3,.h4,.h5,.h6{
    color: #282828;
  }

  .background{
    background: -webkit-linear-gradient(top, #c6dd6b, #fcfff8)
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
    background:  rgba(40, 40, 40, 0.8);
    border-top-left-radius: 10% 50%;
    border-bottom-left-radius: 10% 50%;
    margin-top: 3%;
    padding: 3%;
  }

  .nameApp{
    font-family: computer_pixel-7;
    font-size: 3.5em;
    color: #c6dd6b;
  }

  .register-left{
    text-align: center;
    color: #000000;
    margin-top: 4%;
    }

  .register-right{
    background-color: #fcfff8;
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

  .btn-register{
    background-color: #c6dd6b;
    color: #3d3d3d;
  }

  .error{
    font-size: 1.25em;
  }
</style>
