<template>
  <nav class="navbar navbar-expand-lg border-bottom shadow-lg px-4 nav">
    <router-link class="navbar-brand font-weight-bold nameApp mr-5" to="/">Clocking Monkey</router-link>
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"><i class="fas fa-bars icon-menu"></i></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNavDropdown">
        <ul class="navbar-nav d-flex justify-content-between w-100">
            <li class="nav-item active">
                <router-link class="nav-link font-weight-bold" to="/allowUsers" v-if="admin">Usuarios Permitidos</router-link>
            </li>
            <li class="nav-item dropdown mr-5">
                <a class="nav-link mr-5 font-weight-bold" href="#" id="navbarDropdownMenuLink" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false"><i class="fas fa-cogs mr-1"></i> Opciones <i class="fas fa-caret-down"></i></a>
                <div class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
                    <router-link class="dropdown-item" to="/profile"><i class="fas fa-users-cog"></i> Perfil</router-link>
                    <a class="dropdown-item" href="#" @click="logout"><i class="fas fa-sign-out-alt"></i> Salir</a>
                </div>
            </li>
        </ul>
    </div>
</nav>
</template>

<script>
import firebase from 'firebase'

export default {
  name: 'navbar',
  props: {
    admin: ''
  },
  methods: {
    logout: function () {
      firebase.auth().signOut().then(user => {
        this.$router.go({ path: this.$router.path })
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

  .nav{
    background-color: #3d3d3d;
  }

  .nameApp{
    font-family: computer_pixel-7;
    font-size: 3.5em;
    color: #c6dd6b;
  }

  .nameApp:hover{
    color: #fcfff8;
  }

  .nav-link{
    font-size: 1.15em;
    color: #c6dd6b;
  }

  .nav-link:hover{
    color: #fcfff8;
  }

  .icon-menu{
    font-size: 1.75em;
    color: #c6dd6b;
  }

  .dropdown-menu{
    background-color: #3d3d3d;
  }

  .dropdown-item{
    color: #c6dd6b;
  }

  .dropdown-item:hover{
    color: #282828;
  }
</style>
