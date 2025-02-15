# [EXTRANET AjedrezCoimbra]

> El proyecto estará desarrollado con **Vue, Laravel y MySQL** (front, back y bd) principalmente con otras tecnologías asociadas.

Para ver el README.md en Visual Studio Code pulsa **Ctrl + Shift + V**.

## 📌Laravel con Docker (PHP 8.1 + MySQL + PhpMyAdmin)

Este proyecto configura un entorno de desarrollo para Laravel usando **Docker Compose**, con **PHP 8.1**, **MySQL 8.0**, y **PhpMyAdmin**.

---
## 📌 **Requisitos previos**

Antes de comenzar, asegúrate de tener instalado en tu sistema:
- [Docker](https://www.docker.com/get-started)
- [Docker Compose](https://docs.docker.com/compose/install/)
- [Git](https://git-scm.com/)

Para comenzar a trabajar con este proyecto, primero debes clonar el repositorio privado desde GitHub. Asegúrate de tener configurado tu acceso a GitHub (con SSH o mediante un token de acceso personal si es privado).

### 1️⃣ Clonar el Repositorio
Ejecuta el siguiente comando para clonar el repositorio en tu máquina local:

```bash
git clone https://github.com/JoKiE1982/extranet.git
```

### 2️⃣ Acceder al Directorio del Proyecto
Una vez clonado el repositorio, navega dentro del directorio del proyecto:

```bash
cd extranet
```
---
## 🚀 **Configuración del entorno**

### **1️⃣ Estructura del Proyecto**

```
📁 extranet/
 ├── 📄 docker-compose.yml    # Configuración de Docker
 ├── 📄 Dockerfile            # Configuración de PHP con Composer
 ├── 📄 README.md             # Documentación
 ├── 📁 resources/            # Archivos de Laravel
 ├── 📁 routes/               # Rutas de Laravel
 ├── 📁 storage/              # Almacenamiento de Laravel
 ├── 📄 .env                  # Configuración del entorno
```

### **2️⃣ Crear Dockerfile para PHP con Composer**

Crea un archivo `Dockerfile` en la raíz del proyecto con el siguiente contenido:

```dockerfile
# Usa la imagen oficial de PHP con Apache
FROM php:8.1-apache

# Instalar extensiones necesarias para Laravel
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    zip \
    unzip \
    git \
    curl \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd pdo pdo_mysql

# Instalar Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Habilitar mod_rewrite de Apache (necesario para Laravel)
RUN a2enmod rewrite

# Configurar el directorio de trabajo
WORKDIR /var/www/html
```

### **3️⃣ Crear `docker-compose.yml`**

```yaml
version: "3.8"

services:
  mysql:
    image: mysql:8.0
    container_name: mysql_container
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: extranet
      MYSQL_USER: extranet
      MYSQL_PASSWORD: chesscoimbra
    ports:
      - "3306:3306"
    volumes:
      - db:/var/lib/mysql

  php:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: php_container
    restart: always
    volumes:
      - .:/var/www/html
    ports:
      - "8080:80"
    depends_on:
      - mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin_container
    restart: always
    environment:
      PMA_HOST: mysql
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "8081:80"
    depends_on:
      - mysql

volumes:
  db:
```

### **4️⃣ Iniciar el Entorno con Docker Compose**
Para levantar los contenedores de Docker (PHP, MySQL y PhpMyAdmin), ejecuta el siguiente comando en la raíz del proyecto:

```bash
docker-compose up -d #Esto iniciará los servicios en segundo plano. Puedes ver los logs en tiempo real omitiendo el flag -d:
```

Para detener el entorno:
```bash
docker-compose down
```

### **5️⃣ Configurar Laravel (`.env`)**

Modifica el archivo `.env` en la raíz del proyecto:

```env
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=extranet
DB_USERNAME=extranet
DB_PASSWORD=chesscoimbra
```

### **5️⃣ Configurar permisos para Laravel**

Ejecuta estos comandos para dar permisos correctos a Laravel:

```bash
docker exec -it php_container bash
cd /var/www/html
chmod -R 777 storage bootstrap/cache
exit
```

---
## 🚀 **Instalar y Configurar Vue 3 con Tailwind en Laravel**

### **1️⃣ Instalar Vue 3 y Vite**
Ejecuta este comando en la raíz de tu proyecto Laravel:
```bash
npm install vue@next
```

Luego instala Vite y los complementos necesarios para que Vue funcione con Laravel:
```bash
npm install @vitejs/plugin-vue
```

### **2️⃣ Configurar Vite para Vue**

Abre el archivo `vite.config.js` en la raíz del proyecto Laravel (o créalo si no existe) y agrégale lo siguiente:

```js
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';

export default defineConfig({
    plugins: [vue()],
});
```

### **3️⃣ Instalar Tailwind CSS**

Ejecuta el siguiente comando para instalar Tailwind CSS y sus dependencias:
```bash
npm install -D tailwindcss postcss autoprefixer
```

Luego, genera el archivo de configuración:
```bash
npx tailwindcss init -p
```

### **4️⃣ Configurar Tailwind CSS**

Edita `tailwind.config.js` y asegúrate de incluir las rutas de las plantillas:
```js
module.exports = {
  content: [
    "./resources/**/*.blade.php",
    "./resources/**/*.js",
    "./resources/**/*.vue"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

En `resources/css/app.css`, añade:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### **5️⃣ Configurar Vue en el Proyecto**

Dentro del directorio `resources/js/`, crea un archivo `app.js` con el siguiente contenido:

```js
import { createApp } from 'vue';
import App from './App.vue';
import '../css/app.css';

createApp(App).mount('#app');
```

Y dentro de `resources/js/`, crea un archivo `App.vue` con un componente básico de Vue:

```vue
<template>
    <h1 class="text-3xl font-bold underline">¡Hola desde Vue 3 con Tailwind en Laravel!</h1>
</template>

<script>
export default {
    name: "App"
};
</script>
```

### **6️⃣ Agregar Vue al Blade Template**

Edita el archivo `resources/views/welcome.blade.php` y agrega lo siguiente:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Laravel con Vue 3 y Tailwind</title>
    @vite('resources/js/app.js')
</head>
<body>
    <div id="app"></div>
</body>
</html>
```

### **7️⃣ Compilar y Ejecutar el Proyecto**

Ejecuta este comando para compilar y ver los cambios en vivo:
```bash
npm run dev
```

---
## 🌐 **Acceder a la Aplicación**

✅ Laravel estará disponible en:  
🔗 [http://localhost:8080](http://localhost:8080)

✅ PhpMyAdmin estará en:  
🔗 [http://localhost:8081](http://localhost:8081)

---
## 🛠 Trabajar con Git
### **1️⃣ Subir Cambios al Repositorio**
 
Asegúrate de estar en la rama correcta (por defecto, main o master):
```bash
git checkout main
```

Añade los cambios realizados:
```bash
git add .
```

Realiza un commit con un mensaje descriptivo:
```bash
git commit -m "Descripción de los cambios realizados"
```

Sube los cambios al repositorio remoto:
```bash
git push origin main
```

### **2️⃣ Actualizar el Repositorio Local**
 
Si hay cambios en el repositorio remoto y deseas actualizar tu copia local:
```bash
git pull origin main
```

### **3️⃣ Crear una Nueva Rama**

Si deseas trabajar en una nueva funcionalidad, crea una rama:
```bash
git checkout -b nombre-de-la-rama
```

Luego, sube la rama al repositorio remoto:
```bash
git push origin nombre-de-la-rama
```

## 🎉 **¡Todo listo!**

Ahora tienes Laravel funcionando con Docker, Vue 3 y Tailwind CSS correctamente configurados. 🚀🔥