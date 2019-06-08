<template>
    <div>
        <button @click.prevent="logout">SALIR</button>
    </div>
</template>

<script>
import firebase from 'firebase'

export default {
  name: 'home',
  methods: {
    logout: function () {
      firebase.auth().signOut().then(() => {
        this.$router.go({ path: this.$router.path })
      })
    }
  }
}
</script>
