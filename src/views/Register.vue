<template>
  <v-container class="fill-height" fluid>
    <v-row align="center" justify="center">
      <v-col cols="12" md="4">
        <v-card class="pa-6 elevation-12 rounded-xl">
          <v-card-title class="text-h5 font-weight-bold">Registro</v-card-title>
          <v-card-text>
            <v-text-field
              v-model="username"
              label="Usuario"
              prepend-inner-icon="mdi-account"
            />
            <v-text-field
              v-model="password"
              label="Contraseña"
              prepend-inner-icon="mdi-lock"
              type="password"
            />

            <!-- Botones -->
            <v-btn color="success" class="mt-4" block @click="register">
              Crear cuenta
            </v-btn>
            <v-btn variant="text" class="mt-2" block @click="router.push('/login')">
              ¿Ya tienes cuenta? Inicia sesión
            </v-btn>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import api from '@/services/api'

const username = ref('')
const password = ref('')
const router = useRouter()

const register = async () => {
  try {
    await api.post('/register', {
      username: username.value,
      password: password.value
    })
    router.push('/login')
  } catch {
    alert('No se pudo registrar el usuario')
  }
}
</script>
