<template>
  <v-container class="fill-height" fluid>
    <v-row align="center" justify="center">
      <v-col cols="12" md="4">
        <v-card class="pa-6 elevation-12 rounded-xl">
          <v-card-title class="text-h5 font-weight-bold">Iniciar sesión</v-card-title>
          <v-card-text>
            <v-text-field v-model="username" label="Usuario" prepend-inner-icon="mdi-account" />
            <v-text-field v-model="password" label="Contraseña" prepend-inner-icon="mdi-lock" type="password" />
          </v-card-text>
          <v-card-actions>
            <v-btn color="primary" block @click="login">Entrar</v-btn>
            <v-btn text block @click="router.push('/register')">¿No tienes cuenta? Regístrate</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'

const username = ref('')
const password = ref('')
const router = useRouter()

const login = async () => {
  try {
    const { data } = await axios.post('/api/login', {
      username: username.value,
      password: password.value
    })
    localStorage.setItem('token', data.token)
    router.push('/tasks')
  } catch {
    alert('Credenciales inválidas')
  }
}
</script>
