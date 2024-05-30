<template>
  <v-app light>

    <Navbar v-if="!isLoginPage"/>
    <v-main>

      <router-view></router-view>
      <Bank v-if="isDefault"/>
    </v-main>
    <Footer v-if="!isLoginPage"/>
  </v-app>
</template>
<script>
import Navbar from "@/components/Navbar.vue"
import Footer from "@/components/Footer.vue"
export default {
  data(){
    return{
    }
  },
  components:{
    Navbar,
    Footer,
 
  },
  computed: {
    // Determine if the current page is the login page
    isLoginPage() {
      return this.$route.path === '/login';
    },
    isDefault() {
      return this.$route.path === '/';
    },
   
  },
  created() {
    
  },
  methods:{
    
  }
}
</script>
