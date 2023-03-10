<script setup>
import { ref } from 'vue'
import { startRegistration } from '@simplewebauthn/browser'

const username = ref('username')
const options = ref(null)
const error = ref(null)

async function register(event) {
  if (!username.value) {
    return;
  }

  const optionRequest = await fetch(`http://127.0.0.1:8787/option`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: username.value })
  })

  const optionResponse = await optionRequest.json()

  if (optionRequest.status !== 200) {
    console.log(optionResponse)
    return;
  }

  const registrationResponse = await startRegistration(optionResponse.options)

  const registerRequest = await fetch(`http://127.0.0.1:8787/register`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: username.value, registrationResponse })
  })

  console.log(registerRequest.status)

  const registerResponse = await registerRequest.json()
  console.log(registerResponse)
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
