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
                <input type="text" id="inputName" class="form-control" v-model="name" placeholder="Nombre">
              </div>
              <div class="m-2">
                <input type="text" id="inputFirstLastname" class="form-control" v-model="firstLastname" placeholder="Primer apellido">
              </div>
              <div class="m-2">
                <input type="text" id="inputSecondLasname" class="form-control" v-model="secondLastname" placeholder="Segundo apellido">
              </div>
              <div class="m-2">
                <input type="text" id="inputEmail" class="form-control" disabled v-model="email">
              </div>
              <div class="form-group mx-2 mt-5">
                <button class="btn btn-block p-2" @click.prevent="save">Guardar datos</button>
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
      secondLastname: ''
    }
  },
  computed: {
    email: function () {
      return firebase.auth().currentUser.email
    }
  },
  methods: {
    save: function () {
      let data = {
        name: this.name,
        first_lastname: this.firstLastname,
        second_lastname: this.secondLastname,
        email: this.email
      }

      firebase.firestore().collection('Users').add(data).then(result => {
        this.$router.push({ path: '/' })
      }).catch(error => {
        if (error) {
          console.log(error.message)
        }
      })
    }
  }
}
</script>

<style scope>
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
</style>
