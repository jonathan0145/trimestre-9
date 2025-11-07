# ⚙️ Menú de Scripts - Sistema InmoTech

## Información del Proyecto
- **Proyecto**: InmoTech - Sistema de Chat Inmobiliario
- **Fase**: Implementación Piloto
- **Fecha**: Noviembre 2025
- **Versión**: 1.0

---

## 📋 Índice de Scripts de Inicialización

### 🎯 **Scripts Principales**

#### 1. **[🔐 Crear Permisos](./createPermissions.js)**
   - Crea todos los permisos del sistema
   - Define permisos por módulo (usuarios, propiedades, ofertas, chat, etc.)
   - Inicialización de la matriz de permisos

#### 2. **[👤 Crear Rol Admin](./createAdminRole.js)**
   - Crea el rol de administrador del sistema
   - Define permisos completos para administración
   - Usuario super administrador inicial

#### 3. **[🔗 Asignar Permisos por Rol](./createPermissionsByRole.js)**
   - Asigna permisos específicos a cada rol
   - Configura matriz de permisos por rol (Comprador, Vendedor, Intermediario, Admin)
   - Establece restricciones y accesos

#### 4. **[🌱 Datos de Prueba](./seedTestData.js)**
   - Carga datos de ejemplo para el piloto
   - Usuarios de prueba (Ana Torres, Luis Gómez, Carla Ruiz)
   - Propiedades de ejemplo, ofertas y conversaciones de prueba

#### 5. **[🔍 Verificar Conexión](./verifyConnection.js)**
   - Verifica la conexión con la base de datos
   - Prueba la configuración del sistema
   - Validación de la instalación completa

---

## 🗂️ **Organización por Función**

### **🔧 Configuración Inicial**:
- [Verificar Conexión](./verifyConnection.js) - **Ejecutar PRIMERO**
- [Crear Permisos](./createPermissions.js) - **Ejecutar SEGUNDO**
- [Crear Rol Admin](./createAdminRole.js) - **Ejecutar TERCERO**

### **👥 Gestión de Usuarios**:
- [Asignar Permisos por Rol](./createPermissionsByRole.js)
- [Datos de Prueba](./seedTestData.js)

---

## 🚀 **Guía de Ejecución**

### **Orden Recomendado de Ejecución**:

```bash
# 1. Verificar que todo esté funcionando
node verifyConnection.js

# 2. Crear estructura de permisos
node createPermissions.js

# 3. Crear rol de administrador
node createAdminRole.js

# 4. Asignar permisos a roles
node createPermissionsByRole.js

# 5. Cargar datos de prueba para el piloto
node seedTestData.js
```

### **Pre-requisitos**:
- ✅ MySQL instalado y funcionando
- ✅ Base de datos creada
- ✅ Archivo `.env` configurado
- ✅ Dependencias instaladas (`npm install`)

---

## 📊 **Estado de los Scripts**

| Script | Función | Estado | Dependencias | Tiempo Estimado |
|--------|---------|--------|--------------|-----------------|
| [verifyConnection.js](./verifyConnection.js) | Verificar BD | ✅ Listo | MySQL | 30 seg |
| [createPermissions.js](./createPermissions.js) | Crear permisos | ✅ Listo | BD conectada | 1 min |
| [createAdminRole.js](./createAdminRole.js) | Rol admin | ✅ Listo | Permisos creados | 30 seg |
| [createPermissionsByRole.js](./createPermissionsByRole.js) | Asignar permisos | ✅ Listo | Roles y permisos | 1 min |
| [seedTestData.js](./seedTestData.js) | Datos piloto | ✅ Listo | Estructura completa | 2 min |

---

## 🎯 **Descripción Detallada**

### **[🔍 verifyConnection.js](./verifyConnection.js)**
- **Propósito**: Validar que la conexión a la base de datos funciona correctamente
- **Qué hace**:
  - Prueba la conexión con MySQL
  - Verifica las credenciales del archivo `.env`
  - Muestra información de la base de datos
- **Cuándo usar**: Antes de ejecutar cualquier otro script

### **[🔐 createPermissions.js](./createPermissions.js)**
- **Propósito**: Crear todos los permisos necesarios del sistema
- **Qué hace**:
  - Define permisos para cada módulo (usuarios, propiedades, ofertas, chat, etc.)
  - Crea la estructura base de permisos
  - Establece permisos CRUD (Crear, Leer, Actualizar, Eliminar)
- **Cuándo usar**: Después de verificar la conexión

### **[👤 createAdminRole.js](./createAdminRole.js)**
- **Propósito**: Crear el rol de administrador del sistema
- **Qué hace**:
  - Crea el rol "Administrador" con permisos completos
  - Define el usuario super administrador
  - Establece acceso total al sistema
- **Cuándo usar**: Después de crear los permisos

### **[🔗 createPermissionsByRole.js](./createPermissionsByRole.js)**
- **Propósito**: Asignar permisos específicos a cada rol de usuario
- **Qué hace**:
  - Asigna permisos al rol Comprador
  - Asigna permisos al rol Vendedor
  - Asigna permisos al rol Intermediario
  - Configura restricciones por rol
- **Cuándo usar**: Después de crear roles y permisos

### **[🌱 seedTestData.js](./seedTestData.js)**
- **Propósito**: Cargar datos de ejemplo para el piloto
- **Qué hace**:
  - Crea usuarios de prueba del piloto
  - Carga propiedades de ejemplo
  - Genera ofertas y conversaciones de prueba
  - Prepara el sistema para las pruebas
- **Cuándo usar**: Al final, para preparar el piloto

---

## 🔧 **Comandos Útiles**

### **Ejecución Individual**:
```bash
# Verificar conexión
node scripts/verifyConnection.js

# Crear permisos
node scripts/createPermissions.js

# Crear admin
node scripts/createAdminRole.js

# Asignar permisos
node scripts/createPermissionsByRole.js

# Datos de prueba
node scripts/seedTestData.js
```

### **Ejecución desde Backend**:
```bash
# Si estás en la carpeta del backend
node src/scripts/verifyConnection.js
node src/scripts/createPermissions.js
node src/scripts/createAdminRole.js
node src/scripts/createPermissionsByRole.js
node src/scripts/seedTestData.js
```

---

## 🚨 **Troubleshooting**

### **Problemas Comunes**:

#### **Error de Conexión**:
```
Error: connect ECONNREFUSED 127.0.0.1:3306
```
**Solución**:
1. Verificar que MySQL esté funcionando: `net start mysql`
2. Revisar credenciales en `.env`
3. Confirmar que la base de datos existe

#### **Error de Permisos**:
```
Error: Access denied for user
```
**Solución**:
1. Verificar usuario y contraseña en `.env`
2. Confirmar permisos del usuario en MySQL
3. Revisar que el usuario tenga acceso a la base de datos

#### **Tablas No Existen**:
```
Error: Table 'database.permissions' doesn't exist
```
**Solución**:
1. Ejecutar migraciones primero
2. Verificar que Sequelize haya creado las tablas
3. Revisar configuración de la base de datos

---

## 📞 **Soporte**

### **Para problemas con scripts**:
- **Email**: soporte.tecnico@inmotech.com
- **Documentación**: [Guía de Instalación](../modulo%203%20estrategia%20de%20implantacion/Piloto%20implementación%20en%20área%20pequeña/actividad-2-instalacion-configuracion.md)

### **Para errores de base de datos**:
- **Revisar**: Configuración de `.env`
- **Verificar**: Estado de MySQL
- **Consultar**: Logs del sistema

---

## 📝 **Notas Importantes**

> 📌 **Importante**: Ejecutar los scripts en el orden recomendado para evitar errores de dependencias.

> ⚠️ **Advertencia**: Los scripts de datos de prueba solo deben ejecutarse en entornos de desarrollo/piloto.

> 🔄 **Repetición**: Los scripts pueden ejecutarse múltiples veces de forma segura (verifican existencia antes de crear).

> 📋 **Logs**: Todos los scripts generan logs detallados de su ejecución.

---

**Fecha de creación**: Noviembre 6, 2025  
**Versión**: 1.0  
**Proyecto**: InmoTech - Sistema de Chat Inmobiliario  

> Este menú es parte de los **Scripts de Inicialización** del **Piloto de Implementación en Área Pequeña** de InmoTech.