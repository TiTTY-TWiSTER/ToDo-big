<template>
  <div class="container">
    <div>
      <Logo />
      <router-link to="/page">page</router-link>
      <router-link to="/noPage"> no page</router-link>
    </div>
  </div>
</template>

<script>
import Logo from '~/components/Logo.vue'

export default {
  components: {
    Logo
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}


</style>
