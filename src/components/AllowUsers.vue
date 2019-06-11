<template>
    <div>
        <Navbar :admin="true"/>
        <div class="container mt-5">
            <ul class="list-group">
                <li v-for="user in users" v-bind:key="user" class="list-group-item d-flex justify-content-between align-items-center">
                    <div>
                        <p>{{ user.email }}</p>
                        <span>{{ user.rol }}</span>
                    </div>
                    <div>
                        <button class="btn btn-danger" @click="deleteUser(user)" type="submit">Descativar Usuario</button>
                    </div>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import firebase from 'firebase'
import Navbar from './Navbar'

export default {
  name: 'allowUsers',
  data: function () {
    return {
      users: []
    }
  },
  created () {
    this.loadUsers()
  },
  methods: {
    deleteUser: function (user) {
      const promises = []
      firebase.firestore().collection('Users').where('email', '==', user.email).get().then(query => {
        query.forEach(doc => {
          promises.push(
            doc.ref.update({
              active: false
            })
          )
          return Promise.all(promises)
        })
      }).then(() => {
        firebase.firestore().collection('AllowedUsers').where('email', '==', user.email).get().then(query => {
          query.forEach(doc => {
            doc.ref.delete().then(() => {
              this.loadUsers()
            }, error => {
              if (error) {
                console.log(error.meesage)
              }
            })
          })
        })
      }, error => {
        if (error) {
          console.log(error.message)
        }
      })
    },
    loadUsers: function () {
      this.users = []
      firebase.firestore().collection('AllowedUsers').get().then(query => {
        if (query.size > 0) {
          query.forEach(doc => {
            let data = {
              email: doc.data().email,
              rol: doc.data().rol === 'Administrator' ? 'Administrador' : 'Empleado'
            }
            this.users.push(data)
          })
        }
      })
    }
  },
  components: {
    Navbar
  }
}
</script>
