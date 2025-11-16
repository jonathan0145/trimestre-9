# Guía Completa de Instalación y Configuración - Actividad 2
## Piloto: Implementación en Área Pequeña - Inmotech

### 📋 Objetivo
Realizar la instalación y configuración completa del backend y frontend de Inmotech en un entorno de pruebas, preparando el sistema para la ejecución del piloto con usuarios reales.

---

## 🔧 Preparación del Entorno de Pruebas

### Requisitos Previos
- **Node.js** versión 14 o superior
- **MySQL** versión 5.7 o superior
- **Git** instalado
- **npm** o **yarn** como gestor de paquetes
- **Editor de código** (VS Code recomendado)

### Verificación de Requisitos
```bash
# Verificar versiones instaladas
node --version
npm --version
mysql --version
git --version
```

---

## 📁 Estructura del Proyecto

```
componentes/
├── backend/
│   ├── src/
│   │   ├── config/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── routes/
│   │   ├── middlewares/
│   │   ├── scripts/          # Scripts de inicialización
│   │   └── index.js
│   ├── .env.example
│   └── package.json
└── frontend/
    ├── src/
    ├── public/
    ├── .env.example
    └── package.json
```

---

## 🎯 PROCEDIMIENTO PASO A PASO

### PASO 1: Preparación del Entorno

#### 1.1 Clonar el Repositorio
```bash
# Si aún no tienes el proyecto
git clone [https://github.com/jonathan0145/componentes.git]
cd componentes
```

#### 1.2 Crear Base de Datos
```sql
-- Conectar a MySQL como administrador
mysql -u root -p

-- Crear base de datos para el piloto
CREATE DATABASE inmotech_piloto CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Crear usuario específico para el piloto (opcional pero recomendado)
CREATE USER 'inmotech_piloto'@'localhost' IDENTIFIED BY 'piloto_password_2025';
GRANT ALL PRIVILEGES ON inmotech_piloto.* TO 'inmotech_piloto'@'localhost';
FLUSH PRIVILEGES;

-- Verificar la creación
SHOW DATABASES;
```

---

### PASO 2: Configuración del Backend

#### 2.1 Instalar Dependencias del Backend
```bash
cd backend
npm install
```

#### 2.2 Configurar Variables de Entorno
```bash
# Copiar archivo de ejemplo
cp .env.example .env

# Editar el archivo .env con los valores correctos
# (usar editor de texto o VS Code)
```

**Valores para .env (ejemplo para entorno de pruebas):**
```env
# Base de datos
DB_HOST=localhost
DB_NAME=inmotech_piloto
DB_USER=inmotech_piloto
DB_PASS=piloto_password_2025
DB_PORT=3306

# Servidor
PORT=3000
NODE_ENV=development

# JWT Secret (generar uno único)
JWT_SECRET=piloto_jwt_secret_2025_cambiar_en_produccion

# Email (configurar con credenciales reales)
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=tu_email@gmail.com
EMAIL_PASS=tu_app_password

# CORS
CORS_ORIGIN=http://localhost:3001,http://127.0.0.1:3001

# Frontend URL
FRONTEND_URL=http://localhost:3001
```

#### 2.3 Ejecutar Scripts de Inicialización
```bash
# 1. Crear permisos del sistema
node src/scripts/createPermissions.js

# 2. Crear roles y asignar permisos
node src/scripts/createPermissionsByRole.js

# 3. Crear rol de administrador
node src/scripts/createAdminRole.js

# 4. Cargar datos de ejemplo para el piloto
node src/scripts/seedTestData.js
```

#### 2.4 Verificar y Levantar el Backend
```bash
# Verificar que no hay errores de configuración
npm run dev

# Si todo está bien, debería mostrar:
# ✅ Servidor corriendo en puerto 3000
# ✅ Base de datos conectada
# ✅ Rutas configuradas
```

---

### PASO 3: Configuración del Frontend

#### 3.1 Instalar Dependencias del Frontend
```bash
# En una nueva terminal
cd ../frontend
npm install
```

#### 3.2 Configurar Variables de Entorno del Frontend
```bash
# Copiar archivo de ejemplo
cp .env.example .env

# Editar .env del frontend
```

**Valores para .env del frontend:**
```env
REACT_APP_API_URL=http://localhost:3000/api/v1
REACT_APP_SOCKET_URL=http://localhost:3000
REACT_APP_GOOGLE_MAPS_API_KEY=tu_api_key_google_maps
REACT_APP_GOOGLE_CLIENT_ID=tu_google_client_id
```

#### 3.3 Levantar la Aplicación Frontend
```bash
npm start

# Debería abrir automáticamente en http://localhost:3001
```

---

### PASO 4: Verificación de la Instalación

#### 4.1 Verificar Conexión Frontend-Backend
1. **Abrir navegador en:** `http://localhost:3001`
2. **Verificar que carga** la página principal
3. **Probar registro/login** con credenciales de prueba
4. **Verificar conectividad** en la consola del navegador (F12)

#### 4.2 Credenciales de Prueba para el Piloto
```
COMPRADOR (Ana Torres):
- Email: ana.torres.piloto@inmotech.com
- Password: Piloto2025!
- Rol: buyer

VENDEDOR (Luis Gómez):
- Email: luis.gomez.piloto@inmotech.com
- Password: Piloto2025!
- Rol: seller

INTERMEDIADOR (Carla Ruiz):
- Email: carla.ruiz.piloto@inmotech.com
- Password: Piloto2025!
- Rol: agent

ADMINISTRADOR:
- Email: admin.piloto@inmotech.com
- Password: AdminPiloto2025!
- Rol: admin
```

#### 4.3 Pruebas Funcionales Básicas
1. **Login exitoso** con cada tipo de usuario
2. **Navegación** entre módulos principales
3. **Crear/ver propiedades** (vendedor)
4. **Ver propiedades disponibles** (comprador)
5. **Gestión de usuarios** (admin)
6. **Chat básico** entre usuarios
7. **Ofertas** creación y visualización

---

## ✅ CHECKLIST DE VERIFICACIÓN

### Preparación del Entorno
- [ ] Node.js instalado y funcionando
- [ ] MySQL instalado y funcionando
- [ ] Base de datos `inmotech_piloto` creada
- [ ] Usuario de base de datos configurado

### Configuración Backend
- [ ] Dependencias instaladas (`npm install` exitoso)
- [ ] Archivo `.env` configurado correctamente
- [ ] Scripts de inicialización ejecutados sin errores
- [ ] Datos de ejemplo cargados
- [ ] Backend levantado en puerto 3000
- [ ] Endpoints respondiendo (probar `/api/v1/auth/health`)

### Configuración Frontend
- [ ] Dependencias instaladas (`npm install` exitoso)
- [ ] Archivo `.env` configurado correctamente
- [ ] Frontend levantado en puerto 3001
- [ ] Página principal carga correctamente
- [ ] No hay errores en consola del navegador

### Pruebas de Integración
- [ ] Login exitoso con usuario comprador
- [ ] Login exitoso con usuario vendedor
- [ ] Login exitoso con usuario intermediador
- [ ] Login exitoso con usuario admin
- [ ] Chat entre usuarios funcionando
- [ ] Creación de propiedades (vendedor)
- [ ] Visualización de propiedades (comprador)
- [ ] Creación de ofertas (comprador)
- [ ] Notificaciones básicas funcionando

### Datos de Prueba
- [ ] 4 usuarios de ejemplo creados y verificados
- [ ] 3 propiedades de ejemplo creadas
- [ ] 2 ofertas de ejemplo creadas
- [ ] Relaciones entre datos funcionando correctamente

---

## 🚨 Solución de Problemas Comunes

### Error: Cannot connect to database
```bash
# Verificar que MySQL esté ejecutándose
# En Windows:
net start mysql

# Verificar credenciales en .env
# Probar conexión manual:
mysql -u inmotech_piloto -p inmotech_piloto
```

### Error: Port 3000 already in use
```bash
# Encontrar proceso usando el puerto
netstat -ano | findstr :3000

# Terminar el proceso (reemplazar PID)
taskkill /PID [PID_NUMBER] /F

# O cambiar el puerto en .env
PORT=3001
```

### Error: CORS blocked
- Verificar que `CORS_ORIGIN` en backend incluya la URL del frontend
- Comprobar que ambos servidores estén ejecutándose

### Error en scripts de inicialización
```bash
# Verificar que la base de datos esté creada
# Re-ejecutar scripts en orden:
node src/scripts/createPermissions.js
node src/scripts/createPermissionsByRole.js
node src/scripts/createAdminRole.js
node src/scripts/seedTestData.js
```

---

## 📝 Registro de Incidencias

Durante la instalación, es **OBLIGATORIO** documentar cualquier problema encontrado:

### 📋 Documento de Registro
**Utilizar el documento específico:** [Registro de Incidencias - Instalación](./registro-incidencias-instalacion.md)

### Formato Resumido de Incidencia
```
FECHA: [DD/MM/YYYY HH:MM]
MÓDULO: [Backend/Frontend/Base de datos/Scripts/Configuración]
SEVERIDAD: [Crítica/Alta/Media/Baja]
DESCRIPCIÓN: [Descripción detallada del problema]
PASOS PARA REPRODUCIR: [Pasos específicos numerados]
SOLUCIÓN APLICADA: [Qué se hizo para resolverlo]
TIEMPO INVERTIDO: [Tiempo aproximado]
ESTADO: [Resuelto/Pendiente/Escalado/En progreso]
VERIFICACIÓN: [Cómo se verificó la solución]
```

### ⚠️ Recordatorio Importante
- **Documentar** todas las incidencias, sin excepción
- **Actualizar** el estado regularmente
- **Incluir logs** y mensajes de error específicos
- **Registrar** tiempo invertido para análisis posterior

---

## 🎉 Confirmación Final

### Una vez completados todos los pasos:
1. **Backend ejecutándose** en `http://localhost:3000`
2. **Frontend ejecutándose** en `http://localhost:3001`
3. **4 usuarios de prueba** creados y funcionales
4. **Datos de ejemplo** cargados correctamente
5. **Conexión frontend-backend** verificada
6. **Funcionalidades básicas** probadas y funcionando

### ✅ **El entorno de pruebas está listo para el piloto**

---

## 📞 Contactos de Soporte

- **Equipo Técnico:** [contacto_tecnico@inmotech.com]
- **Líder de Proyecto:** [lider_proyecto@inmotech.com]
- **Documentación:** [README completo en `componentes/documentacion/`]
- **Registro de Incidencias:** [Documento específico](./registro-incidencias-instalacion.md)

---

**Fecha de creación:** 31/10/2025  
**Versión:** 1.0  
**Última actualización:** 31/10/2025  

> Este documento es parte de la **Actividad 2: Instalación y configuración de backend y frontend en entorno de pruebas** del **Piloto de Implementación en Área Pequeña** de Inmotech.