<script setup>
import { ref } from 'vue'
import { startRegistration, startAuthentication } from '@simplewebauthn/browser'

const username = ref('username')
const options = ref(null)
const error = ref(null)

async function register(event) {
  if (!username.value) {
    error.value = 'Insert an username'
    return
  }

  const optionRequest = await fetch(`http://127.0.0.1:8787/register/options`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: username.value })
  })

  const optionResponse = await optionRequest.json()

  if (optionRequest.status !== 200) {
    error.value = optionResponse.oops ?? 'Something went wrong'
    return
  }

  const registrationResponse = await startRegistration(optionResponse.options)

  const registerRequest = await fetch(`http://127.0.0.1:8787/register/verify`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: username.value, registrationResponse })
  })

  if (registerRequest.status !== 200) {
    error.value = optionResponse.oops ?? 'Something went wrong'
    return
  }

  const registerResponse = await registerRequest.json()
  console.log(registerResponse)
}

async function login(event) {
  if (!username.value) {
    error.value = 'Insert an username'
    return
  }

  const loginOptionRequest = await fetch(`http://127.0.0.1:8787/login/options`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: username.value })
  })

  const loginOptionResponse = await loginOptionRequest.json()

  if (loginOptionRequest.status !== 200) {
    error.value = loginOptionResponse.oops ?? 'Something went wrong'
    return
  }

  const authenticationResponse = await startAuthentication(loginOptionResponse.options)

  const verificationRequest = await fetch(`http://localhost:8787/login/verify`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: username.value, authenticationResponse })
  })

  const verificationResponse = await verificationRequest.json()

  if (verificationRequest.status !== 200) {
    error.value = verificationResponse.oops ?? 'Something went wrong'
    return
  }

  console.log(verificationResponse)
}

function hideOops(event) {
  error.value = null
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
          <p class="buttons is-justify-content-center">
            <button class="button is-primary" type="submit" @click="login">Login</button>
            <button class="button is-info" type="submit" @click="register">Register</button>
          </p>
        </div>
        <article class="message is-danger mt-5" v-if="!!error">
          <div class="message-header">
            <p>Oops</p>
            <button class="delete" aria-label="delete" @click="hideOops"></button>
          </div>
          <div class="message-body">
            {{ error }}
          </div>
        </article>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
