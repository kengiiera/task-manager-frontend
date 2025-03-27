<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-toolbar-title>Prueba técnica</v-toolbar-title>
      <v-spacer />
      <v-btn text @click="logout">Cerrar sesión</v-btn>
    </v-app-bar>
    <v-main><router-view /></v-main>
  </v-app>
</template>
<script setup>
import { useRouter } from 'vue-router'
const router = useRouter()
const logout = () => {
  localStorage.removeItem('token')
  router.push('/login')
}
</script>
