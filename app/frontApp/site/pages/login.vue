<template>
   <v-app class="mt-12">
      <v-main>
         <v-container fluid fill-height>
            <v-layout align-center justify-center>
               <v-flex xs12 sm8 md4>
                  <v-card class="elevation-12">
                     <v-toolbar dark color="#0c426f">
                        <v-toolbar-title dark color = "primary">{{isRegister ? stateObj.register.name : stateObj.login.name}} Form</v-toolbar-title>
                     </v-toolbar>
                     <v-card-text>
                     <form ref="form" @submit.prevent="isRegister ? register() : login()">
                            <v-text-field
                              v-model="username"
                              name="username"
                              label="Username"
                              type="text"
                              placeholder="username"
                              required
                           ></v-text-field>
                           <v-text-field v-if="isRegister"
                              v-model="email"
                              name="email"
                              label="E-mail"
                              type="email"
                              placeholder="E-mail"
                              required
                           ></v-text-field>
                            <v-text-field
                              v-model="password"
                              name="password"
                              label="Password"
                              type="password"
                              placeholder="password"
                              required
                           ></v-text-field>

                           <v-text-field v-if="isRegister"
                              v-model="confirmPassword"
                              name="confirmPassword"
                              label="Confirm Password"
                              type="password"
                              placeholder="cocnfirm password"
                              required
                           ></v-text-field>
                           <div class="red--text"> {{errorMessage}}</div>
                           <v-btn type="submit" class="mt-4" color="primary" value="Log in">{{isRegister ? stateObj.register.name : stateObj.login.name}}</v-btn>
                           <div class="grey--text mt-4" v-on:click="isRegister = !isRegister;">
                              {{toggleMessage}}  
                           </div>
                      </form>
                     </v-card-text>
                  </v-card>
                
               </v-flex>
            </v-layout>
         </v-container>
      </v-main>
   </v-app>
</template>

<script>
import Vue from 'vue'
import VueCookies from 'vue-cookies';

export default {
  name: "Login",
  data() {
    return {
      username: "",
      password: "",
      email: "",
      confirmPassword: "",
      isRegister : false,
      errorMessage: "",
      stateObj: {
         register :{
            name: 'Register',
            message: 'Aleady have an Account? login.'
         },
         login : {
            name: 'Login',
            message: 'Register'
         }
      }
    };
  },
  methods: {
    async login() {
      Vue.use(VueCookies);
      let resp = null;
      const { username } = this;
         const options = {
         method: 'POST',
         headers: {'content-type': 'application/json'},
         body: '{"identifier":"'+this.username+'","password":"'+this.password+'"}'
         };

         await fetch('http://localhost:1337/api/auth/local', options)
         .then(async response => {
            if (response.status !== 400) {
                  fetch('http://localhost:1337/api/auth/local', options)
                  .then(response => response.json())
                  .then(response => Vue.prototype.$userData = response)
                  .then(setTimeout(() => { this.$cookies.set("username",Vue.prototype.$userData.user.username, "5h") }, 100))
                  .then(setTimeout(() => { this.$cookies.set("jwt",Vue.prototype.$userData.jwt, "5h") }, 100))
                  
                  .then(window.location.href = 'http://localhost:3000')

            }
            else{
                  this.errorMessage = "Email or Password are wrong"
            }
         }
         )
   },
    register() { 
       if(this.password == this.confirmPassword){
          this.isRegister = false;
          this.errorMessage = "";
          const options = {
            method: 'POST',
            headers: {'content-type': 'application/json'},
            body: '{"username":"'+this.username+'","email":"'+this.email+'","password":"'+this.password+'"}'
          };

         fetch('http://localhost:1337/api/auth/local/register', options)
         .then(response => response.json())
         .then(response => console.log(response))
         .then(response => Vue.prototype.$userData = response)
         this.$router.push('/')
      }
       else {
         this.errorMessage = "Passwords did not match"
       }
    },
  },
      computed: {
       toggleMessage : function() { 
          return this.isRegister ? this.stateObj.register.message : this.stateObj.login.message }
    }
};
</script>