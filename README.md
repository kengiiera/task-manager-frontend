# 🗂️ Task Manager - Frontend

Este es el **frontend** del proyecto *Task Manager*, desarrollado como parte de la prueba técnica. Construido con **Vue 3 + Vuetify**, permite gestionar tareas con autenticación JWT y visualización tipo Kanban.

---

## 🚀 Tecnologías utilizadas

- ✅ [Vue 3](https://vuejs.org/)
- 🎨 [Vuetify 3](https://vuetifyjs.com/)
- 🌐 [Vue Router](https://router.vuejs.org/)
- 🔗 [Axios](https://axios-http.com/)
- ⚙️ [Vite](https://vitejs.dev/)

---

## 🛠️ Instalación y ejecución

1. Clona el repositorio:

   ```bash
   git clone https://github.com/kengiiera/task-manager-frontend.git
   ```

2. Instala las dependencias:

   ```bash
   npm install
   ```

3. Ejecuta el servidor de desarrollo:

   ```bash
   npm run dev
   ```

4. Accede desde tu navegador:

---

## 🔐 Funcionalidades principales

- 🔒 **Autenticación JWT**: Registro e inicio de sesión con token
- 👥 **Control de roles**: `ADMIN` puede ver todas las tareas, `USER` solo las propias
- 📋 **CRUD de tareas**: Crear, listar, actualizar y eliminar
- 🧱 **Vista Kanban**: Visualización de tareas por estado (`PENDING`, `IN_PROGRESS`, `COMPLETED`)
- ✅ **Validaciones**: Formularios con validaciones amigables
- 🔄 **Cierre de sesión** y persistencia con `localStorage`
- 🔔 **Mensajes visuales**: alertas, loaders y confirmaciones

---

## 📁 Estructura del proyecto

```
taskmanager-frontend/
├── src/
│   ├── components/       # Componentes reutilizables (cards, formularios)
│   ├── views/            # Vistas principales (Login, Registro, Kanban)
│   ├── router/           # Rutas protegidas
│   ├── services/         # Comunicación con API backend
│   └── App.vue
├── public/
├── package.json
└── vite.config.js
```

---

## ⚙️ Configuración

Asegúrate de que el backend esté corriendo en `http://localhost:8080`.  
La URL base de la API está definida en:

```
src/services/api.js
```

```js
const api = axios.create({
  baseURL: 'http://localhost:8080/api',
  headers: {
    'Content-Type': 'application/json',
  },
});
```


## 🧠 Prueba técnica

Este proyecto fue desarrollado por **Kengie** como parte de una prueba técnica para el rol **Desarrollador Senior UX**.  
Complementa el backend basado en **Spring Boot** y **Arquitectura Limpia** con autenticación JWT, roles, migraciones y pruebas unitarias.

