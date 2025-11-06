<template>
  <div class="app_container">

    <router-view />

    <Navbar />
    <p>{{ m }}</p>

  </div>
</template>

<script>
import Navbar from './components/Navbar.vue'

export default {
  name: 'App',
  components: {
    Navbar
  },
  data(){
    return{
       
    m : 'User:'+window.Telegram?.WebApp?.initDataUnsafe?.user
    }
  }
}
</script>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  height: 98vh;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  /* Общий стилизованный фон в стиле iOS (можно доработать) */
  background: linear-gradient(to bottom right, #f0f0f0, #ffffff);
}
</style>