<template>
    <div>
        <form class="index.html">
            <input type="email" placeholder="Email" v-model="email"/>
            <input type="password" placeholder="Password" v-model="password"/>
            <button @click.prevent="register">¡REGISTRATE!</button>
            <button @click.prevent="login">INICIAR SESIÓN!</button>
        </form>
    </div>
</template>

<script>
/* eslint-disable */
import firebase from 'firebase';

export default {
    name: 'login',
    data: function() {
        return {
            email: '',
            password: ''
        };
    },
    methods: {
        login: function(){
            firebase.auth().signInWithEmailAndPassword(this.email, this.password).then(user => {
                this.$router.go({ path: '/' });
            },err => {
                console.log(err.message);
            });
        },
        register: function(){
            firebase.firestore().collection("AllowedUsers").where("email", "==", this.email).get().then(query => {
                if (query.size   > 0){
                    firebase.auth().createUserWithEmailAndPassword(this.email, this.password).then(user => {
                        this.$router.go({ path: '/' });
                    },err => {
                        console.log(err.message);
                    });
                }
            },err => {
                console.log(err.message);
            })
        }
    }
}
</script>
