# Checklist de Funcionalidades - Piloto InmoTech

## Información del Checklist
- **Proyecto**: InmoTech - Sistema de Chat Inmobiliario
- **Fase**: Piloto en Área Pequeña
- **Actividad**: Ejecución de Pruebas Funcionales y Técnicas
- **Fecha de Ejecución**: Noviembre 2025
- **Responsables**: Usuarios Piloto y Equipo Técnico

---

## 🎯 Instrucciones de Uso

### **Para Usuarios Piloto**:
- ✅ Marcar cada funcionalidad después de probarla
- 📝 Anotar observaciones y problemas encontrados
- ⭐ Calificar experiencia del 1 al 5 (5 = excelente)
- 🕒 Registrar tiempo aproximado de ejecución

### **Para Equipo Técnico**:
- 🔍 Verificar aspectos técnicos de cada funcionalidad
- 📊 Medir rendimiento y tiempos de respuesta
- 🔒 Validar seguridad y permisos
- 📋 Documentar incidencias técnicas

---

## 👤 **FUNCIONALIDADES DE COMPRADOR (Ana Torres)**

### **🔐 Autenticación y Acceso**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Login con email y contraseña | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Recuperación de contraseña | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Acceso al dashboard personalizado | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Logout seguro | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Sesión se mantiene durante navegación
- ⬜ Tokens JWT funcionando correctamente
- ⬜ Redirección automática tras login exitoso
- ⬜ Validación de credenciales en backend

---

### **🔍 Búsqueda y Filtrado de Propiedades**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Búsqueda básica por ubicación | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Filtro por rango de precio | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Filtro por número de habitaciones | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Filtro por área (m²) | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Filtro por tipo de propiedad | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Vista de resultados en lista | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Vista de resultados en mapa | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Ordenamiento por precio/fecha | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Resultados cargan en < 2 segundos
- ⬜ Filtros se aplican correctamente
- ⬜ Paginación funciona sin errores
- ⬜ URLs son compartibles con filtros

---

### **📱 Sistema de Mensajería y Chat**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Iniciar conversación con vendedor | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Enviar mensajes de texto | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Recibir respuestas en tiempo real | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Enviar imágenes (JPG, PNG) | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Enviar documentos (PDF) | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Ver historial de conversaciones | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Recibir notificaciones de mensajes | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Marcar conversaciones como leídas | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ WebSockets funcionando correctamente
- ⬜ Archivos se suben sin errores
- ⬜ Límites de tamaño respetados (5MB)
- ⬜ Notificaciones push activas

---

### **💰 Sistema de Ofertas**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Acceder al formulario de oferta | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Ingresar monto de oferta | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Especificar términos de pago | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Establecer fecha de cierre | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Agregar condiciones especiales | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Revisar oferta antes de enviar | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Confirmar y enviar oferta | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Ver estado de ofertas enviadas | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Validación de campos numéricos
- ⬜ Notificación automática al vendedor
- ⬜ Estado de oferta actualizado en tiempo real
- ⬜ Historial de ofertas se mantiene

---

### **⭐ Gestión de Favoritos**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Marcar propiedad como favorita | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Ver lista de propiedades favoritas | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Remover propiedad de favoritos | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Organizar favoritos por categorías | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Compartir favoritos por email | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Favoritos se sincronizan entre sesiones
- ⬜ Límite de 20 favoritos respetado
- ⬜ Cambios se guardan instantáneamente
- ⬜ Iconos de favoritos actualizados en tiempo real

---

## 🏢 **FUNCIONALIDADES DE VENDEDOR (Luis Gómez)**

### **🏠 Gestión de Propiedades**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Acceder a "Mis Propiedades" | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Crear nueva publicación | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Completar información básica | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Subir fotografías (mín. 5) | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Escribir descripción detallada | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Establecer precio de venta | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Publicar propiedad | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Editar propiedades existentes | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Pausar/activar publicaciones | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Formulario valida campos obligatorios
- ⬜ Imágenes se redimensionan automáticamente
- ⬜ Propiedad aparece en búsquedas inmediatamente
- ⬜ Editor de texto enriquecido funciona

---

### **📬 Gestión de Consultas y Mensajes**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Recibir notificaciones de consultas | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Ver lista de conversaciones activas | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Responder mensajes de compradores | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Compartir información adicional | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Enviar documentos de propiedad | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Marcar conversaciones prioritarias | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Archivar conversaciones cerradas | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Notificaciones llegan inmediatamente
- ⬜ Contador de mensajes no leídos funciona
- ⬜ Archivos adjuntos se envían correctamente
- ⬜ Búsqueda en conversaciones disponible

---

### **💼 Gestión de Ofertas**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Recibir notificación de nueva oferta | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Ver detalles completos de la oferta | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Evaluar términos propuestos | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Aceptar oferta directamente | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Rechazar oferta con justificación | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Hacer contraoferta | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Ver historial de ofertas | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Estado de ofertas actualizado automáticamente
- ⬜ Comprador recibe respuesta inmediatamente
- ⬜ Validación de montos y fechas
- ⬜ Respaldo de todas las transacciones

---

### **📅 Programación de Visitas**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Acceder a calendario integrado | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Proponer horarios disponibles | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Confirmar citas con compradores | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Recibir recordatorios automáticos | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Reagendar visitas si necesario | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Marcar visitas como completadas | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Calendario sincronizado correctamente
- ⬜ Recordatorios por email funcionando
- ⬜ Zona horaria manejada apropiadamente
- ⬜ Integración con calendarios externos

---

## 🤝 **FUNCIONALIDADES DE INTERMEDIARIO (Carla Ruiz)**

### **👥 Mediación en Conversaciones**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Recibir invitación para mediar | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Revisar contexto de conversación | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Unirse a conversación activa | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Acceder a historial completo | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Facilitar comunicación entre partes | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Proponer soluciones intermedias | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Documentar acuerdos alcanzados | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Rol de mediador claramente identificado
- ⬜ Permisos especiales funcionando
- ⬜ Historial completo accesible
- ⬜ Notificaciones a todas las partes

---

### **📊 Generación de Reportes**

| Funcionalidad | Estado | Tiempo | Calificación | Observaciones |
|---------------|--------|--------|--------------|---------------|
| Acceder a módulo de reportes | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Seleccionar período de análisis | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Generar reporte de conversaciones | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Ver métricas de mediación | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Exportar reportes en PDF | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |
| Compartir reportes con equipo | ⬜ | ___min | ⭐⭐⭐⭐⭐ | |

**Aspectos Técnicos a Verificar**:
- ⬜ Datos precisos y actualizados
- ⬜ Gráficos cargan correctamente
- ⬜ Exportación funciona sin errores
- ⬜ Permisos de visualización adecuados

---

## 🔧 **ASPECTOS TÉCNICOS GENERALES**

### **⚡ Rendimiento**

| Aspecto | Estado | Medición | Criterio | Observaciones |
|---------|--------|----------|-----------|---------------|
| Tiempo de carga inicial | ⬜ | ___seg | < 3 seg | |
| Respuesta de API de búsqueda | ⬜ | ___ms | < 500ms | |
| Envío de mensajes | ⬜ | ___ms | < 200ms | |
| Subida de archivos | ⬜ | ___seg | < 10 seg | |
| Generación de reportes | ⬜ | ___seg | < 15 seg | |

### **🔒 Seguridad**

| Aspecto | Estado | Observaciones |
|---------|--------|---------------|
| Autenticación JWT funciona | ⬜ | |
| Permisos por rol respetados | ⬜ | |
| Datos encriptados en tránsito | ⬜ | |
| Validación de archivos subidos | ⬜ | |
| Protección contra XSS | ⬜ | |
| Protección CSRF | ⬜ | |

### **📱 Compatibilidad**

| Aspecto | Estado | Observaciones |
|---------|--------|---------------|
| Chrome (última versión) | ⬜ | |
| Firefox (última versión) | ⬜ | |
| Safari (última versión) | ⬜ | |
| Edge (última versión) | ⬜ | |
| Móvil Android | ⬜ | |
| Móvil iOS | ⬜ | |

---

## 📋 **Resumen de Resultados**

### **Estadísticas por Usuario**:

**👤 Comprador (Ana Torres)**:
- Total de funcionalidades: ___/34
- Funcionalidades exitosas: ___
- Calificación promedio: ___/5
- Tiempo total de pruebas: ___ horas

**🏢 Vendedor (Luis Gómez)**:
- Total de funcionalidades: ___/28
- Funcionalidades exitosas: ___
- Calificación promedio: ___/5
- Tiempo total de pruebas: ___ horas

**🤝 Intermediario (Carla Ruiz)**:
- Total de funcionalidades: ___/13
- Funcionalidades exitosas: ___
- Calificación promedio: ___/5
- Tiempo total de pruebas: ___ horas

### **Aspectos Técnicos**:
- Rendimiento exitoso: ___/5
- Seguridad validada: ___/6
- Compatibilidad: ___/6

---

## 🏆 **Criterios de Aceptación Global**

### **✅ APROBADO SI:**
- 95% o más de funcionalidades funcionan correctamente
- Calificación promedio de usuarios ≥ 4.0/5
- Todos los aspectos de seguridad validados
- Rendimiento cumple con criterios establecidos

### **⚠️ APROBADO CON OBSERVACIONES SI:**
- 90-94% de funcionalidades funcionan correctamente
- Calificación promedio de usuarios ≥ 3.5/5
- Problemas menores identificados y documentados

### **❌ NO APROBADO SI:**
- Menos del 90% de funcionalidades funcionan
- Calificación promedio < 3.5/5
- Problemas críticos de seguridad o rendimiento

---

## 📝 **Observaciones Generales**

**Fortalezas Identificadas**:
- _________________________________
- _________________________________
- _________________________________

**Áreas de Mejora**:
- _________________________________
- _________________________________
- _________________________________

**Incidencias Críticas**:
- _________________________________
- _________________________________
- _________________________________

**Recomendaciones**:
- _________________________________
- _________________________________
- _________________________________

---

**Fecha de ejecución**: _____________  
**Completado por**: _____________  
**Revisado por**: _____________  
**Aprobado por**: _____________  

---

**Fecha de creación**: Noviembre 6, 2025  
**Versión**: 1.0  
**Proyecto**: InmoTech - Sistema de Chat Inmobiliario  

> Este checklist es parte de la **Actividad 4: Ejecución de pruebas funcionales y técnicas** del **Piloto de Implementación en Área Pequeña** de InmoTech.