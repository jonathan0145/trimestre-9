# Registro de Incidencias - Instalación y Configuración
## Actividad 2: Piloto Implementación en Área Pequeña - Inmotech

### 📋 Información General
- **Proyecto:** Inmotech - Sistema Inmobiliario
- **Fase:** Piloto en Área Pequeña
- **Actividad:** Instalación y Configuración de Backend y Frontend
- **Responsable:** Equipo Técnico
- **Fecha de inicio:** [DD/MM/2025]

---

## 📝 Plantilla de Registro de Incidencias

### Incidencia #001
- **FECHA:** [DD/MM/YYYY HH:MM]
- **MÓDULO:** [Backend/Frontend/Base de datos/Scripts/Configuración]
- **SEVERIDAD:** [Crítica/Alta/Media/Baja]
- **REPORTADO POR:** [Nombre del técnico]
- **DESCRIPCIÓN:** 
  [Descripción detallada del problema encontrado]
- **PASOS PARA REPRODUCIR:**
  1. [Paso específico 1]
  2. [Paso específico 2]
  3. [Paso específico 3]
- **COMPORTAMIENTO ESPERADO:** [Qué debería suceder]
- **COMPORTAMIENTO ACTUAL:** [Qué está sucediendo]
- **ARCHIVOS AFECTADOS:** [Lista de archivos involucrados]
- **LOGS/ERRORES:** 
  ```
  [Copiar aquí los logs o mensajes de error específicos]
  ```
- **SOLUCIÓN APLICADA:** [Qué se hizo para resolverlo]
- **TIEMPO INVERTIDO:** [Tiempo aproximado para resolver]
- **ESTADO:** [Resuelto/Pendiente/Escalado/En progreso]
- **VERIFICACIÓN:** [Cómo se verificó que quedó resuelto]
- **NOTAS ADICIONALES:** [Información adicional relevante]

---

## 🔄 Registro de Incidencias Documentadas

### Incidencia #001 - Ejemplo
- **FECHA:** 01/11/2025 09:30
- **MÓDULO:** Base de datos
- **SEVERIDAD:** Media
- **REPORTADO POR:** Técnico 1
- **DESCRIPCIÓN:** 
  Error al conectar con la base de datos durante la ejecución de scripts de inicialización
- **PASOS PARA REPRODUCIR:**
  1. Ejecutar `node src/scripts/createPermissions.js`
  2. Observar error de conexión a MySQL
- **COMPORTAMIENTO ESPERADO:** Script se ejecuta sin errores y crea permisos
- **COMPORTAMIENTO ACTUAL:** Error "ECONNREFUSED" en conexión MySQL
- **ARCHIVOS AFECTADOS:** 
  - `src/config/database.js`
  - `.env`
- **LOGS/ERRORES:** 
  ```
  Error: connect ECONNREFUSED 127.0.0.1:3306
      at TCPConnectWrap.afterConnect [as oncomplete]
  ```
- **SOLUCIÓN APLICADA:** 
  1. Verificar que MySQL esté ejecutándose: `net start mysql`
  2. Verificar credenciales en archivo `.env`
  3. Reiniciar servicio MySQL
- **TIEMPO INVERTIDO:** 15 minutos
- **ESTADO:** Resuelto
- **VERIFICACIÓN:** Script ejecutado exitosamente, permisos creados en DB
- **NOTAS ADICIONALES:** Agregar verificación de servicio MySQL en guía

---

### Incidencia #002 - [Título descriptivo]
- **FECHA:** [DD/MM/YYYY HH:MM]
- **MÓDULO:** [Módulo afectado]
- **SEVERIDAD:** [Nivel]
- **REPORTADO POR:** [Nombre]
- **DESCRIPCIÓN:** 
  [Descripción detallada]
- **PASOS PARA REPRODUCIR:**
  1. [Paso 1]
  2. [Paso 2]
- **COMPORTAMIENTO ESPERADO:** [Expectativa]
- **COMPORTAMIENTO ACTUAL:** [Realidad]
- **ARCHIVOS AFECTADOS:** [Lista de archivos]
- **LOGS/ERRORES:** 
  ```
  [Logs aquí]
  ```
- **SOLUCIÓN APLICADA:** [Solución implementada]
- **TIEMPO INVERTIDO:** [Tiempo]
- **ESTADO:** [Estado actual]
- **VERIFICACIÓN:** [Método de verificación]
- **NOTAS ADICIONALES:** [Información adicional]

---

### Incidencia #003 - [Título descriptivo]
- **FECHA:** [DD/MM/YYYY HH:MM]
- **MÓDULO:** [Módulo afectado]
- **SEVERIDAD:** [Nivel]
- **REPORTADO POR:** [Nombre]
- **DESCRIPCIÓN:** 
  [Descripción detallada]
- **PASOS PARA REPRODUCIR:**
  1. [Paso 1]
  2. [Paso 2]
- **COMPORTAMIENTO ESPERADO:** [Expectativa]
- **COMPORTAMIENTO ACTUAL:** [Realidad]
- **ARCHIVOS AFECTADOS:** [Lista de archivos]
- **LOGS/ERRORES:** 
  ```
  [Logs aquí]
  ```
- **SOLUCIÓN APLICADA:** [Solución implementada]
- **TIEMPO INVERTIDO:** [Tiempo]
- **ESTADO:** [Estado actual]
- **VERIFICACIÓN:** [Método de verificación]
- **NOTAS ADICIONALES:** [Información adicional]

---

## 📊 Resumen de Incidencias

### Por Módulo
- **Backend:** [Número] incidencias
- **Frontend:** [Número] incidencias  
- **Base de datos:** [Número] incidencias
- **Scripts:** [Número] incidencias
- **Configuración:** [Número] incidencias

### Por Severidad
- **Críticas:** [Número] incidencias
- **Altas:** [Número] incidencias
- **Medias:** [Número] incidencias
- **Bajas:** [Número] incidencias

### Por Estado
- **Resueltas:** [Número] incidencias
- **Pendientes:** [Número] incidencias
- **En progreso:** [Número] incidencias
- **Escaladas:** [Número] incidencias

### Tiempo Total Invertido
- **Total:** [X] horas [Y] minutos
- **Promedio por incidencia:** [Z] minutos

---

## 🔍 Análisis de Patrones

### Problemas Más Frecuentes
1. [Tipo de problema más común]
2. [Segundo problema más común]
3. [Tercer problema más común]

### Lecciones Aprendidas
- [Lección 1]: [Descripción]
- [Lección 2]: [Descripción]
- [Lección 3]: [Descripción]

### Mejoras Propuestas para la Guía
1. [Mejora 1]: [Descripción de la mejora necesaria]
2. [Mejora 2]: [Descripción de la mejora necesaria]
3. [Mejora 3]: [Descripción de la mejora necesaria]

---

## 📞 Contactos para Escalación

### Nivel 1 - Soporte Técnico
- **Email:** soporte.tecnico@inmotech.com
- **Teléfono:** +57 300 XXX XXXX
- **Horario:** Lunes a Viernes, 8:00 AM - 6:00 PM

### Nivel 2 - Líder Técnico
- **Email:** lider.tecnico@inmotech.com
- **Teléfono:** +57 301 XXX XXXX
- **Disponibilidad:** 24/7 para incidencias críticas

### Nivel 3 - Arquitecto de Sistemas
- **Email:** arquitecto.sistemas@inmotech.com
- **Disponibilidad:** Bajo solicitud para incidencias críticas

---

## 📚 Referencias y Documentación

- [Guía Completa de Instalación](./actividad-2-instalacion-configuracion.md)
- [Documentación Técnica del Proyecto](../../componentes/documentacion/)
- [Scripts de Inicialización](../../componentes/backend/src/scripts/)
- [Configuraciones de Entorno](../../componentes/backend/.env.example)
////////////////////////////////////// solo el primero funciona ///////////////////////////////////

---

## 📝 Instrucciones de Uso

### Cómo Reportar una Incidencia
1. **Copiar** la plantilla de incidencia (#001)
2. **Asignar** número consecutivo
3. **Completar** todos los campos obligatorios
4. **Documentar** con el máximo detalle posible
5. **Actualizar** el resumen estadístico
6. **Notificar** al responsable correspondiente

### Cómo Dar Seguimiento
1. **Actualizar** el estado regularmente
2. **Documentar** cada intento de solución
3. **Registrar** tiempo invertido
4. **Verificar** que la solución funciona
5. **Cerrar** la incidencia cuando esté resuelta

---

**Fecha de creación:** 01/11/2025  
**Versión:** 1.0  
**Última actualización:** 01/11/2025  

> Este documento es parte de la **Actividad 2: Instalación y configuración de backend y frontend en entorno de pruebas** del **Piloto de Implementación en Área Pequeña** de Inmotech.