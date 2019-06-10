<template>
    <div class="container centered">
        <div class="login shadow rounded w-75">
            <div class="row">
                <div class="col-md-4 login-left">
                    <img src="../../media/clockingmonkey_logo.png" alt="Logo Clocking Monkey" class="w-75">
                    <h2 class="nameApp">Clocking Monkey</h2>
                </div>
                <div class="col-md-8 login-right p-3">
                    <div class="center">
                        <h3 class="title_login">Bienvenido</h3>
                        <h6>Accede a tu cuenta</h6>
                    </div>
                    <form action="index.html" class="col-md-12 mt-3">
                        <div class="col-auto mt-2">
                            <label class="sr-only" for="inputEmail">Email</label>
                            <div class="input-group mb-2">
                                <div class="input-group-prepend">
                                    <div class="input-group-text"><i class="fas fa-user"></i></div>
                                </div>
                                <input type="email" class="form-control" id="inputEmail" placeholder="Email" v-model="email">
                            </div>
                        </div>
                        <div class="col-auto mt-2">
                            <label class="sr-only" for="inputPassword">Password</label>
                            <div class="input-group mb-2">
                                <div class="input-group-prepend">
                                    <div class="input-group-text"><i class="fas fa-key"></i></div>
                                </div>
                                <input type="password" class="form-control" id="inputPassword" placeholder="Password" v-model="password">
                            </div>
                        </div>
                        <div class="alert alert-danger mt-3" v-if="error">
                            <span class="error">{{ message }}</span>
                        </div>
                        <div class="mx-5 my-3">
                          <button class="btn btn-block" @click.prevent="login">Iniciar sesión</button>
                        </div>
                        <div class="mx-5 my-3">
                          <router-link class="btn btn-block" to="/register">¡Regístrate!</router-link>
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
  name: 'login',
  data: function () {
    return {
      email: '',
      password: '',
      message: '',
      error: false
    }
  },
  methods: {
    login: function () {
      firebase.auth().signInWithEmailAndPassword(this.email, this.password).then(user => {
        this.$router.go({ path: this.$router.path })
      }, error => {
        if (error) {
          this.message = 'Esta dirección de correo electrónico no está registrada'
          this.error = true
        }
      })
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
        align-items: center;
        text-align: center;
        min-height: 100vh;
    }

    .login{
        background: rgba(104,159,56,0.5);
        margin-top: 3%;
        padding: 3%;
    }

    .nameApp{
        font-family: computer_pixel-7;
        font-size: 3.5em;
        color: #ffffff;
    }

    .login-left{
        text-align: center;
        color: #000000;
        margin-top: 4%;
    }

    .login-right{
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
