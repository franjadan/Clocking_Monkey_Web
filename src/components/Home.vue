<template>
  <div class="background">
    <Navbar class="mb-5" :admin="admin"/>
    <div class="container mt-5 mb-5">
        <h3 class="my-2">{{ user }}</h3>
        <hr/>
        <h2 class="mt-2">Control de asistencia</h2>
        <grid :data="assists" :columns="columns" :keys="keys" :admin="admin"/>
    </div>
  </div>
</template>

<script>
import firebase from 'firebase'
import Grid from './Grid'
import Navbar from './Navbar'

export default {
  name: 'home',
  data: function () {
    return {
      assists: [],
      columns: ['Usuario', 'Fecha', 'Tipo', 'Comentario'],
      keys: ['user', 'date', 'type', 'comment', 'fail'],
      admin: false,
      user: ''
    }
  },
  created () {
    firebase.firestore().collection('Users').where('email', '==', firebase.auth().currentUser.email).get().then(query => {
      if (query.size > 0) {
        this.user = 'Bienvenido ' + query.docs[0].data().name + ' ' + query.docs[0].data().first_lastname
      }
    })
    firebase.firestore().collection('AllowedUsers').where('email', '==', firebase.auth().currentUser.email).get().then(query => {
      if (query.size > 0) {
        if (query.docs[0].data().rol === 'Administrator') {
          this.admin = true
          firebase.firestore().collection('Assists').orderBy('date', 'desc').get().then(query => {
            if (query.size > 0) {
              this.saveAssists(query)
            }
          })
        } else {
          firebase.firestore().collection('Assists').where('email', '==', firebase.auth().currentUser.email).orderBy('date', 'desc').get().then(query => {
            if (query.size > 0) {
              this.saveAssists(query)
            }
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
        let email = doc.data().email
        let assistance = ('0' + date.getDate()).slice(-2) + '/' + ('0' + (date.getMonth() + 1)).slice(-2) + '/' + date.getFullYear() + ' ' + ('0' + date.getHours()).slice(-2) + ':' + ('0' + date.getMinutes()).slice(-2)
        let type = doc.data().type === true ? 'Entrada' : 'Salida'
        let fail = doc.data().fail
        let comment = doc.data().comment
        firebase.firestore().collection('Users').where('email', '==', email).get().then(query => {
          let name = query.docs[0].data().name + ' ' + query.docs[0].data().first_lastname
          let data = {
            user: name,
            date: assistance,
            type: type,
            comment: comment,
            fail: fail
          }
          this.assists.push(data)
        })
      })
    },
    getUser: function (email) {
      firebase.firestore().collection('Users').where('email', '==', email).get().then(query => {
        if (query.size > 0) {
          console.log(query.docs[0].data().name + ' ' + query.docs[0].data().first_lastname + ' ' + query.docs[0].data().second_lastname)
        }
      })
    }
  },
  components: {
    Grid,
    Navbar
  }
}
</script>

<style scoped>
  h1,h2,h3,h4,h5,h6{
    color: #282828;
  }

  .h1,.h2,.h3,.h4,.h5,.h6{
    color: #282828;
  }

  .background{
    background-color: #fcfff8;
  }
</style>
