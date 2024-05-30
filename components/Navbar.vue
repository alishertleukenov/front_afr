<template>
  <nav>
    <v-app-bar height="100px" app>

      <img @click="routetomain" src="@/assets/output-onlinepngtools.png" alt=""
        style="max-width: 240px; cursor: pointer">
      <input id="searchName" type="text" @keyup.enter="search()" class="input" v-model="input" placeholder="Поиск"
        style="margin-left: 50px" />
      <v-spacer></v-spacer>

      <v-btn title='Пользователь' text color='white' class="custom-button">
        <v-icon left>mdi-account</v-icon>
        <span>{{ this.username}}</span>
      </v-btn>
      <v-btn key="logout" title='Выйти из аккаунта' text color='white' @click="logout">
        <span>Выйти</span>
      </v-btn>
      <!--      <v-btn-->
      <!--        key="canvas"-->
      <!--        title='Выйти из аккаунта'-->
      <!--        text-->
      <!--        @click="canvas"-->
      <!--      >-->
      <!--        <span>canvas</span>-->
      <!--      </v-btn>-->
    </v-app-bar>
    <v-navigation-drawer v-model="drawer" app temporary style="width: max-content">
      <v-layout column>
        <v-flex class="mt-5" style="display: flex; justify-content: center">
          <!--          <v-avatar size="100">-->
          <!--            <img src="@/static/user.png" style="max-width: 50px" alt=""/>-->
          <!--          </v-avatar>-->
          <v-icon style="margin: 0 7px 6px 0">mdi-account</v-icon>
          <p class="black--text subheading mt-3 text-center">{{this.username}}</p>
        </v-flex>
        <!--        <v-app-bar-nav-icon @click.stop='drawer = !drawer' style="margin-top: 10px; margin-right: 10px; align-self: self-end"></v-app-bar-nav-icon>-->
        
      </v-layout>
    </v-navigation-drawer>
  </nav>
</template>
  
  <script>
  import axios from 'axios';
  axios.defaults.xsrfHeaderName = "X-CSRFTOKEN";
  axios.defaults.xsrfCookieName = "csrftoken";
  export default {
    name: "Navbar",
    data: () => ({
      drawer: false,
      input: '',
      username: ''
    }),
    mounted() {
      if (this.$auth.loggedIn) {
        this.username = this.$auth.user.full_name
        console.log('loggedin');
        console.log(this.username);
        console.log(this.$auth.user);
      }
    },
    computed:{
      user(){
        console.log(this.$auth.user)
        return this.$auth.user.full_name
      },
      
    },
    created(){
      this.checkup()
    },
    methods:{
      async search() {
        console.log('/api/search/' + this.input)
        const response = await this.$axios.get('/api/search/?search=' + this.input, {
        });
        console.log(this.input)
        this.$router.push({
          path: '/search_results',
          query: { input: this.input },
        })
      },
      checkup(){
  
        console.log(this.$auth.user)
      },
      async logout() {
        try {
          console.log('Starting logout...');
          const refreshToken = localStorage.getItem('refreshToken');
          let accessToken = localStorage.getItem('accessToken');
          console.log('Tokens from localStorage:', refreshToken, accessToken);

          // Обновление accessToken с использованием refreshToken
          const response = await this.$axios.post('/api/token/refresh/', { refresh: refreshToken });
          console.log('Token refreshed:', response.data);
          accessToken = response.data.access;

          console.log('refreshToken ', refreshToken);
          console.log('accessToken ', accessToken);

          // Вызываем endpoint для логаута на бэкенде с передачей refresh токена
          const logoutResponse = await this.$axios.post('/api/token/logout/', {
            refresh: refreshToken
          }, {
            headers: {
              'Authorization': `Bearer ${accessToken}`
            }
          });
          console.log('Logout response:', logoutResponse);

          // Удаление токенов из localStorage
          localStorage.removeItem('refreshToken');
          localStorage.removeItem('accessToken');

          // Выполнение логаута на фронтенде и перенаправление на страницу входа
          await this.$auth.logout();
          this.$router.push('/login');
        } catch (error) {
          console.error('Logout failed:', error);
          // Обработка ошибок, возможно показ сообщения пользователю или другие действия
        }
      },

      routetomain(){
        this.$router.push('/main')
      },
  
    }
  }
  </script>
  
  <style scoped>
  input {
    display: block;
    width: 350px;
    margin: 20px auto;
    padding: 10px 45px;
    background: white no-repeat 15px center;
    background-size: 15px 15px;
    font-size: 16px;
    border: none;
    border-radius: 5px;
    box-shadow: rgba(50, 50, 93, 0.25) 0px 2px 5px -1px,
    rgba(0, 0, 0, 0.3) 0px 1px 3px -1px;
  }
  </style>
  