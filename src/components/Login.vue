<template>
    <div class="background">
        <div class="container centered">
            <div class="login shadow-lg w-75">
                <div class="row d-flex align-items-center">
                    <div class="col-md-4 login-left">
                        <img src="../../media/clockingmonkey_logo.png" alt="Logo Clocking Monkey" class="w-75">
                        <h2 class="nameApp">Clocking Monkey</h2>
                    </div>
                    <div class="col-md-8 login-right p-3">
                        <div class="center my-4">
                            <h2 class="title-login mt-1 mb-1">Bienvenido</h2>
                            <h4 class="mt-1 mb-1">Accede a tu cuenta</h4>
                        </div>
                        <form action="index.html" class="col-md-12 my-4">
                            <div class="col-auto mt-3 mb-3">
                                <label class="sr-only mt-1 mb-1" for="inputEmail">Email</label>
                                <div class="input-group mb-2">
                                    <div class="input-group-prepend">
                                        <div class="input-group-text"><i class="fas fa-user"></i></div>
                                    </div>
                                    <input type="email" class="form-control" id="inputEmail" placeholder="Email" v-model="email">
                                </div>
                            </div>
                            <div class="col-auto mt-3 mb-3">
                                <label class="sr-only mt-1 mb-1" for="inputPassword">Password</label>
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
                            <div class="mx-5 mt-4 mb-4">
                                <button class="btn btn-block btn-login font-weight-bold" @click.prevent="login">Iniciar sesión</button>
                            </div>
                            <div class="mx-5 mt-4 mb-4">
                                <router-link class="btn btn-block btn-register font-weight-bold" to="/register">¡Regístrate!</router-link>
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
        align-items: center;
        text-align: center;
        min-height: 100vh;
    }

    .login{
        background: rgba(40, 40, 40, 0.8);
        border-top-left-radius: 10% 50%;
        border-bottom-left-radius: 10% 50%;
        margin-top: 3%;
        padding: 3%;
    }

    .nameApp{
        font-family: computer_pixel-7;
        font-size: 3.5em;
        color:  #c6dd6b;
    }

    .login-left{
        text-align: center;
        margin-top: 4%;
    }

    .login-right{
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

    .btn-login{
        background-color: #c6dd6b;
        color: #3d3d3d;
    }

    .btn-register{
        background-color: #3d3d3d;
        color: #c6dd6b;
    }

    .error{
        font-size: 1.25em;
    }
</style>
