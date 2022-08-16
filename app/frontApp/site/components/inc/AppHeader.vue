<template>
    <v-container id = 'main-page' >
          <v-app-bar color="#0c426f" dark clipped-left app>
            <v-app-bar-nav-icon @click=openClose(drawer)></v-app-bar-nav-icon>
             <v-toolbar-title>Γραφείο Μεταφοράς Τεχνολογίας</v-toolbar-title>
             <v-spacer></v-spacer>

                <v-toolbar-title v-if="this.jwt" class="mr-3">{{this.$cookies.get("username")}}</v-toolbar-title>
                <v-toolbar-title v-else></v-toolbar-title>


              <v-btn v-if="this.jwt" @click="logout()" icon>
                <v-icon class="mr-3">mdi-logout</v-icon>
              </v-btn>


              <v-btn v-else icon to = "login">
                <v-icon class="mr-3">mdi-login</v-icon>
              </v-btn>

              
              
          </v-app-bar>
          <v-navigation-drawer clipped app v-model="drawer" permanent v-if="drawer">
            <v-list nav dense>
              <v-list-item-group active-class="#0c426f--text text--accent-4">
                <v-list-item to = "/">
                  <v-list-item-icon>
                    <v-icon>mdi-home</v-icon>
                  </v-list-item-icon>
                  <v-list-item-title>Αρχική σελίδα</v-list-item-title>
                </v-list-item>
                <v-list-item>
                  <v-list-item-icon>
                    <v-icon>mdi-account</v-icon>
                  </v-list-item-icon>
                  <v-list-item-title>Ο Λογαριασμός μου</v-list-item-title>
                </v-list-item>
              </v-list-item-group>
            </v-list>
          </v-navigation-drawer>
    </v-container>
</template>
<script>
import Vue from 'vue'
import VueCookies from 'vue-cookies';

export default {
  name: "AppHeader",
  data: () => ({
    drawer: false,
    jwt: false
  }),
  beforeMount(){
    Vue.use(VueCookies);

    if(this.$cookies.get("jwt") !== null) {
        this.jwt = true
      }
    else if (this.$cookies.get("jwt") !== null){
        this.jwt = false
      }
      
  },

  methods:{
      logout(){
      Vue.use(VueCookies);
      this.$cookies.remove('jwt');
      this.$cookies.remove('username');
      window.location.href = 'http://localhost:3000'
    },

    openClose(drawer){
        if(drawer === true){
          this.drawer = false
        }
        else{
          this.drawer = true
        }
    }
  }
};
</script>







