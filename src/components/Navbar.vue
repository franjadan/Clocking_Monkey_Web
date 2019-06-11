<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light border-bottom shadow-sm py-3">
    <a class="navbar-brand" href="#">Clocking Monkey</a>
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNavDropdown">
        <ul class="navbar-nav d-flex justify-content-between w-100">
            <li class="nav-item active ml-5">
                <router-link class="nav-link" to="/allowUsers" v-if="admin">Usuarios Permitidos</router-link>
            </li>
            <li class="nav-item dropdown mr-5">
                <a class="nav-link mr-5" href="#" id="navbarDropdownMenuLink" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false"><i class="fas fa-cogs mr-1"></i> Opciones <i class="fas fa-caret-down"></i></a>
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
