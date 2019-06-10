<template>
    <div class="container mt-5">
        <button @click.prevent="logout">SALIR</button>
        <h5>{{ welcome }}</h5>
        <h6>Control de asistencia</h6>
        <grid :data="assists" :columns="columns" :keys="keys"/>
    </div>
</template>

<script>
import firebase from 'firebase'
import Grid from './Grid'

export default {
  name: 'home',
  data: function () {
    return {
      assists: [],
      columns: ['Usuario', 'Fecha', 'Tipo'],
      keys: ['email', 'date', 'type'],
      admin: false,
      user: ''
    }
  },
  created () {
    firebase.firestore().collection('Users').where('email', '==', firebase.auth().currentUser.email).get().then(query => {
      if (query.size > 0) {
        this.user = query.docs[0].data().name + ' ' + query.docs[0].data().first_lastname
      }
    })
    firebase.firestore().collection('AllowedUsers').where('email', '==', firebase.auth().currentUser.email).get().then(query => {
      if (query.size > 0) {
        if (query.docs[0].data().rol === 'Administrator') {
          firebase.firestore().collection('Assists').get().then(query => {
            this.saveAssists(query)
          })
        } else {
          firebase.firestore().collection('Assists').where('email', '==', firebase.auth().currentUser.email).get().then(query => {
            this.saveAssists(query)
          })
        }
      }
    })
  },
  methods: {
    logout: function () {
      firebase.auth().signOut().then(() => {
        this.$router.go({ path: this.$router.path })
      })
    },
    saveAssists: function (query) {
      query.forEach(doc => {
        let date = new Date(doc.data().date.seconds * 1000)
        let data = {
          email: doc.data().email,
          date: date.getDate() + '/' + (date.getMonth() + 1) + '/' + date.getFullYear() + ' ' + date.getHours() + ':' + date.getMinutes(),
          type: doc.data().type
        }
        this.assists.push(data)
      })
    }
  },
  components: {
    Grid
  },
  computed: {
    welcome: function () {
      return 'Bienvenido ' + this.user
    }
  }
}
</script>
