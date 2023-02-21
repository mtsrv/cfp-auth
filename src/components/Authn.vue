<script setup>
import { ref } from 'vue'

const username = ref('username')
const error = ref(null)
const data = ref(null)

function register(event) {
  if (!username.value) {
    return;
  }

  fetch(`https://google.com`)
    .then((res) => res.json())
    .then((json) => (data.value = json))
    .catch((err) => (error.value = err))
}
</script>

<template>
  <div class="hero is-fullheight">
    <div class="hero-body is-justify-content-center is-align-items-center">
      <div class="columns is-flex is-flex-direction-column box">
        <div class="column">
          <input class="input is-primary" type="text" placeholder="username" v-model="username">
        </div>
        <div class="column">
          <button class="button is-primary is-fullwidth" type="submit" @click="register">Login</button>
        </div>
        <div class="has-text-centered">
          <p class="is-size-7"> Don't have an account? <a href="#" class="has-text-primary">Sign up</a></p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
