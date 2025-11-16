# Plan de Casos de Prueba - Piloto InmoTech

## Información del Plan
- **Proyecto**: InmoTech - Sistema de Chat Inmobiliario
- **Fase**: Piloto en Área Pequeña
- **Actividad**: Ejecución de Pruebas Funcionales y Técnicas
- **Responsable**: Equipo de Pruebas
- **Fecha**: Noviembre 2025

---

## 🎯 Objetivos de las Pruebas

### **Funcionales**:
- Validar que todos los módulos funcionen correctamente
- Verificar flujos de trabajo completos por rol
- Identificar problemas de usabilidad

### **Técnicas**:
- Probar integración backend-frontend
- Validar rendimiento y tiempos de respuesta
- Verificar seguridad y permisos

---

## 👥 Usuarios Piloto de Prueba

| Usuario | Rol | Email | Responsabilidades |
|---------|-----|-------|------------------|
| **Ana Torres** | Comprador | ana.torres.piloto@inmotech.com | Casos de búsqueda y ofertas |
| **Luis Gómez** | Vendedor | luis.gomez.piloto@inmotech.com | Casos de gestión y ventas |
| **Carla Ruiz** | Intermediario | carla.ruiz.piloto@inmotech.com | Casos de mediación |
| **Admin Sistema** | Administrador | admin.piloto@inmotech.com | Supervisión técnica |

---

## 📋 Casos de Prueba por Perfil

### 🏠 **COMPRADOR (Ana Torres)**

#### **CP-C001: Autenticación y Acceso**
- **Descripción**: Validar proceso de login y acceso al sistema
- **Precondiciones**: Usuario registrado en el sistema
- **Pasos**:
  1. Navegar a la página de login
  2. Ingresar credenciales válidas
  3. Hacer clic en "Iniciar Sesión"
  4. Verificar redirección al dashboard de comprador
- **Resultado Esperado**: Acceso exitoso al dashboard personalizado
- **Criterios de Aceptación**: 
  - Login exitoso en < 3 segundos
  - Dashboard muestra información específica del comprador

#### **CP-C002: Búsqueda de Propiedades**
- **Descripción**: Probar funcionalidad de búsqueda y filtros
- **Precondiciones**: Usuario autenticado como comprador
- **Pasos**:
  1. Ir a sección "Propiedades"
  2. Realizar búsqueda básica por ubicación
  3. Aplicar filtros (precio, habitaciones, área)
  4. Ver resultados y detalles de propiedades
- **Resultado Esperado**: Resultados precisos según filtros aplicados
- **Criterios de Aceptación**:
  - Resultados en < 2 segundos
  - Filtros funcionan correctamente
  - Fotos y detalles se cargan completamente

#### **CP-C003: Iniciar Conversación**
- **Descripción**: Contactar vendedor a través del chat
- **Precondiciones**: Propiedad seleccionada, vendedor disponible
- **Pasos**:
  1. Seleccionar propiedad de interés
  2. Hacer clic en "Contactar Vendedor"
  3. Escribir mensaje inicial
  4. Enviar mensaje
  5. Verificar que vendedor recibe notificación
- **Resultado Esperado**: Conversación creada exitosamente
- **Criterios de Aceptación**:
  - Mensaje enviado instantáneamente
  - Vendedor recibe notificación
  - Conversación aparece en lista de chats

#### **CP-C004: Envío de Archivos en Chat**
- **Descripción**: Compartir documentos e imágenes en conversación
- **Precondiciones**: Conversación activa con vendedor
- **Pasos**:
  1. Abrir conversación existente
  2. Hacer clic en botón de adjuntar archivo
  3. Seleccionar imagen (JPG < 5MB)
  4. Seleccionar documento (PDF < 5MB)
  5. Verificar envío exitoso
- **Resultado Esperado**: Archivos compartidos correctamente
- **Criterios de Aceptación**:
  - Subida exitosa en < 10 segundos
  - Vista previa funcionando
  - Vendedor puede descargar archivos

#### **CP-C005: Realizar Oferta Formal**
- **Descripción**: Hacer oferta económica por propiedad
- **Precondiciones**: Conversación establecida, información de propiedad revisada
- **Pasos**:
  1. En conversación activa, clic en "Hacer Oferta"
  2. Completar formulario de oferta:
     - Monto ofrecido
     - Términos de pago
     - Fecha de cierre
     - Condiciones especiales
  3. Revisar y confirmar oferta
  4. Enviar oferta formal
- **Resultado Esperado**: Oferta registrada y notificada al vendedor
- **Criterios de Aceptación**:
  - Formulario se completa sin errores
  - Vendedor recibe notificación inmediata
  - Oferta aparece con estado "Pendiente"

#### **CP-C006: Gestión de Favoritos**
- **Descripción**: Guardar y gestionar propiedades favoritas
- **Precondiciones**: Usuario autenticado, propiedades disponibles
- **Pasos**:
  1. Explorar propiedades disponibles
  2. Marcar propiedades como favoritas (⭐)
  3. Ir a sección "Mis Favoritos"
  4. Verificar lista de favoritos
  5. Remover algunas propiedades de favoritos
- **Resultado Esperado**: Favoritos se gestionan correctamente
- **Criterios de Aceptación**:
  - Agregar/quitar favoritos funciona
  - Lista actualizada en tiempo real
  - Máximo 20 favoritos permitidos

---

### 🏢 **VENDEDOR (Luis Gómez)**

#### **CP-V001: Gestión de Propiedades**
- **Descripción**: Publicar y gestionar propiedades en venta
- **Precondiciones**: Usuario autenticado como vendedor
- **Pasos**:
  1. Ir a "Mis Propiedades"
  2. Hacer clic en "Publicar Nueva Propiedad"
  3. Completar información básica:
     - Tipo de propiedad
     - Dirección
     - Precio
     - Área
     - Habitaciones/baños
  4. Subir fotografías (mínimo 5)
  5. Agregar descripción detallada
  6. Publicar propiedad
- **Resultado Esperado**: Propiedad publicada exitosamente
- **Criterios de Aceptación**:
  - Formulario se completa sin errores
  - Fotos se suben correctamente
  - Propiedad aparece en búsquedas

#### **CP-V002: Recibir y Gestionar Consultas**
- **Descripción**: Atender mensajes de compradores interesados
- **Precondiciones**: Propiedad publicada, compradores activos
- **Pasos**:
  1. Recibir notificación de nueva consulta
  2. Acceder a sección de "Mensajes"
  3. Abrir conversación con comprador
  4. Leer consulta inicial
  5. Responder con información solicitada
  6. Proponer programar visita
- **Resultado Esperado**: Comunicación fluida con comprador
- **Criterios de Aceptación**:
  - Notificaciones llegan inmediatamente
  - Respuestas se envían sin demora
  - Información se comparte eficientemente

#### **CP-V003: Responder a Ofertas**
- **Descripción**: Evaluar y responder ofertas formales
- **Precondiciones**: Oferta formal recibida de comprador
- **Pasos**:
  1. Recibir notificación de nueva oferta
  2. Revisar detalles de la oferta:
     - Monto ofrecido
     - Condiciones propuestas
     - Términos de pago
  3. Evaluar propuesta
  4. Seleccionar respuesta:
     - Aceptar oferta
     - Rechazar con justificación
     - Hacer contraoferta
  5. Confirmar decisión
- **Resultado Esperado**: Respuesta registrada y comunicada
- **Criterios de Aceptación**:
  - Respuesta en máximo 48 horas
  - Comprador recibe notificación
  - Estado de oferta se actualiza

#### **CP-V004: Programar Visitas**
- **Descripción**: Coordinar citas para mostrar propiedad
- **Precondiciones**: Conversación activa, comprador interesado
- **Pasos**:
  1. En conversación, proponer horarios disponibles
  2. Usar herramienta de calendario integrada
  3. Seleccionar fecha y hora de visita
  4. Confirmar cita con comprador
  5. Recibir recordatorio automático
- **Resultado Esperado**: Visita programada exitosamente
- **Criterios de Aceptación**:
  - Calendario sincronizado
  - Recordatorios automáticos funcionando
  - Confirmación mutua registrada

---

### 🤝 **INTERMEDIARIO (Carla Ruiz)**

#### **CP-I001: Unirse a Conversación**
- **Descripción**: Intervenir como mediador en negociación
- **Precondiciones**: Conversación activa comprador-vendedor
- **Pasos**:
  1. Recibir invitación para mediar
  2. Revisar contexto de la conversación
  3. Aceptar invitación de mediación
  4. Presentarse en la conversación
  5. Revisar historial de mensajes
- **Resultado Esperado**: Integración exitosa como mediador
- **Criterios de Aceptación**:
  - Acceso completo al historial
  - Todas las partes notificadas
  - Rol de mediador claramente identificado

#### **CP-I002: Facilitar Negociación**
- **Descripción**: Asistir en proceso de negociación
- **Precondiciones**: Mediador agregado a conversación
- **Pasos**:
  1. Analizar posiciones de comprador y vendedor
  2. Identificar puntos de convergencia
  3. Proponer soluciones intermedias
  4. Facilitar comunicación entre partes
  5. Documentar acuerdos alcanzados
- **Resultado Esperado**: Negociación progresa positivamente
- **Criterios de Aceptación**:
  - Comunicación neutral y profesional
  - Propuestas constructivas
  - Progreso hacia acuerdo

#### **CP-I003: Generar Reportes**
- **Descripción**: Crear reportes de actividad y métricas
- **Precondiciones**: Conversaciones y transacciones en proceso
- **Pasos**:
  1. Acceder a sección de "Reportes"
  2. Seleccionar período de análisis
  3. Generar reporte de actividad:
     - Conversaciones mediadas
     - Ofertas procesadas
     - Tasa de éxito
  4. Exportar reporte en PDF
- **Resultado Esperado**: Reportes generados correctamente
- **Criterios de Aceptación**:
  - Datos precisos y actualizados
  - Exportación funcional
  - Visualización clara de métricas

---

## 🔧 Casos de Prueba Técnicas

### **CPT-001: Rendimiento del Sistema**
- **Descripción**: Validar tiempos de respuesta
- **Pasos**:
  1. Medir tiempo de carga inicial de la aplicación
  2. Cronometrar respuesta de APIs principales
  3. Probar con múltiples usuarios simultáneos
- **Criterios**: Carga < 3 segundos, APIs < 500ms

### **CPT-002: Seguridad y Permisos**
- **Descripción**: Verificar restricciones por rol
- **Pasos**:
  1. Intentar acceder a funciones no autorizadas
  2. Probar endpoints protegidos
  3. Validar cifrado de datos sensibles
- **Criterios**: Accesos denegados correctamente

### **CPT-003: Integración Backend-Frontend**
- **Descripción**: Probar comunicación entre componentes
- **Pasos**:
  1. Validar flujo completo de datos
  2. Probar manejo de errores
  3. Verificar sincronización en tiempo real
- **Criterios**: Datos consistentes, errores manejados

---

## 📊 Criterios de Éxito General

### **Funcionales**:
- ✅ 95% de casos de prueba exitosos
- ✅ Flujos principales completados sin errores críticos
- ✅ Satisfacción de usuarios > 4/5

### **Técnicos**:
- ✅ Tiempo de respuesta < 500ms
- ✅ Disponibilidad > 99%
- ✅ Sin vulnerabilidades de seguridad críticas

### **Usabilidad**:
- ✅ Tareas completadas en tiempo esperado
- ✅ Interfaz intuitiva y fácil de navegar
- ✅ Documentación clara y suficiente

---

## 📋 Herramientas y Responsables

### **Herramientas de Prueba**:
- **Frontend**: Cypress, Testing Library, Consola del navegador
- **Backend**: Postman, Jest, Supertest
- **Documentación**: Markdown, capturas de pantalla

### **Responsables**:
- **Ejecución Funcional**: Usuarios piloto
- **Ejecución Técnica**: Equipo de desarrollo
- **Documentación**: Responsable de QA

---

**Fecha de creación**: Noviembre 6, 2025  
**Versión**: 1.0  
**Proyecto**: InmoTech - Sistema de Chat Inmobiliario  

> Este plan es parte de la **Actividad 4: Ejecución de pruebas funcionales y técnicas** del **Piloto de Implementación en Área Pequeña** de InmoTech.