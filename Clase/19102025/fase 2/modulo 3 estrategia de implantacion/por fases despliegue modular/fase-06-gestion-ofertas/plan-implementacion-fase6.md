# Plan de Implementación - Fase 6: Gestión de Ofertas y Negociación

## Información de la Fase

**Nombre de la Fase:** Gestión de Ofertas y Negociación
**Número de Fase:** 6
**Fecha de Inicio:** 05/02/2026
**Fecha de Fin:** 11/02/2026
**Responsable Principal:** Carmen López (Desarrollador Backend)

---

## Objetivos de la Fase

### Objetivo Principal
Implementar un sistema completo de gestión de ofertas y negociación que permita a compradores, vendedores y agentes gestionar ofertas inmobiliarias con flujos de negociación estructurados, notificaciones automáticas y seguimiento completo del proceso.

### Objetivos Específicos
- [ ] Desarrollar sistema CRUD completo para gestión de ofertas
- [ ] Implementar flujos de negociación con contrapropuestas
- [ ] Crear sistema de notificaciones en tiempo real
- [ ] Integrar completamente con sistema de propiedades y usuarios
- [ ] Implementar validaciones de negocio y reglas de oferta
- [ ] Crear interfaces de gestión para diferentes tipos de usuarios
- [ ] Implementar reportes y analíticas de ofertas

---

## Componentes a Implementar

### Backend
**Controladores:**
- [ ] `offerController.js`: CRUD de ofertas, gestión de estados
- [ ] `negotiationController.js`: Manejo de procesos de negociación
- [ ] `offerValidationController.js`: Validaciones especializadas
- [ ] `offerNotificationController.js`: Gestión de notificaciones

**Modelos:**
- [ ] Extensión del modelo `Offer.js` con campos avanzados
- [ ] `NegotiationHistory.js`: Historial de negociación
- [ ] `OfferCondition.js`: Condiciones específicas de ofertas
- [ ] `OfferDocument.js`: Documentos asociados a ofertas

**Servicios:**
- [ ] `offerService.js`: Lógica de negocio para ofertas
- [ ] `negotiationService.js`: Procesos de negociación
- [ ] `offerValidationService.js`: Validaciones complejas
- [ ] `offerNotificationService.js`: Notificaciones automáticas
- [ ] `offerReportService.js`: Generación de reportes

**Rutas:**
- [ ] `offerRoutes.js`: Endpoints de gestión de ofertas
- [ ] `negotiationRoutes.js`: Endpoints de negociación
- [ ] `offerReportsRoutes.js`: Endpoints de reportes

### Frontend
**Páginas:**
- [ ] `OffersPage.js`: Panel principal de ofertas
- [ ] `OfferDetailsPage.js`: Detalles completos de oferta
- [ ] `NegotiationPage.js`: Página de proceso de negociación
- [ ] `OfferReportsPage.js`: Reportes y analíticas
- [ ] `OfferManagementPage.js`: Gestión para administradores

**Componentes:**
- [ ] `MakeOfferModal.js`: Formulario para crear ofertas
- [ ] `OfferCard.js`: Tarjeta de información de oferta
- [ ] `NegotiationTimeline.js`: Línea de tiempo de negociación
- [ ] `OfferStatusBadge.js`: Indicador visual de estado
- [ ] `OfferFilters.js`: Filtros de búsqueda
- [ ] `CounterOfferModal.js`: Modal para contrapropuestas
- [ ] `OfferDocuments.js`: Gestión de documentos

**Redux/State:**
- [ ] `offersSlice.js`: Estado de gestión de ofertas
- [ ] `negotiationSlice.js`: Estado de procesos de negociación

---

## Actividades Detalladas

### 1. Extensión de Modelo de Ofertas
**Responsable:** Miguel Rodríguez
**Duración:** 8 horas
**Fecha:** 05/02/2026

**Tareas:**
- [ ] Extender modelo Offer con campos avanzados
- [ ] Crear modelo NegotiationHistory
- [ ] Implementar modelo OfferCondition
- [ ] Configurar relaciones complejas entre modelos
- [ ] Crear migraciones para nuevos campos
- [ ] Escribir tests para modelos extendidos

### 2. Backend - Controladores y Lógica de Negocio
**Responsable:** Carmen López
**Duración:** 18 horas
**Fecha:** 05/02/2026 - 07/02/2026

**Tareas:**
- [ ] Implementar offerController con CRUD avanzado
- [ ] Desarrollar negotiationController para flujos
- [ ] Crear servicios de validación de ofertas
- [ ] Implementar sistema de notificaciones automáticas
- [ ] Desarrollar lógica de estados y transiciones
- [ ] Configurar endpoints de reportes y analíticas

### 3. Frontend - Interfaces de Usuario
**Responsable:** Patricia Jiménez + David Chen
**Duración:** 20 horas
**Fecha:** 07/02/2026 - 09/02/2026

**Tareas:**
- [ ] Crear OffersPage con panel interactivo
- [ ] Desarrollar MakeOfferModal con validaciones
- [ ] Implementar OfferDetailsPage con información completa
- [ ] Crear NegotiationTimeline para seguimiento
- [ ] Desarrollar sistema de filtros y búsqueda
- [ ] Implementar componentes de gestión de documentos

### 4. Sistema de Notificaciones y Tiempo Real
**Responsable:** Ricardo Fernández
**Duración:** 12 horas
**Fecha:** 08/02/2026 - 09/02/2026

**Tareas:**
- [ ] Integrar notificaciones en tiempo real con Socket.io
- [ ] Implementar notificaciones push para mobile
- [ ] Crear sistema de emails automáticos
- [ ] Desarrollar notificaciones en panel
- [ ] Configurar alertas de vencimiento de ofertas
- [ ] Testing de notificaciones de extremo a extremo

### 5. Integración y Validaciones Avanzadas
**Responsable:** Carmen López + David Chen
**Duración:** 14 horas
**Fecha:** 09/02/2026 - 10/02/2026

**Tareas:**
- [ ] Integrar con sistema de propiedades existente
- [ ] Implementar validaciones de reglas de negocio
- [ ] Crear sistema de permisos para ofertas
- [ ] Desarrollar funcionalidades de exportación
- [ ] Implementar cache para optimización
- [ ] Configurar logging y auditoría

### 6. Testing y Optimización
**Responsable:** Carlos Vega
**Duración:** 10 horas
**Fecha:** 10/02/2026 - 11/02/2026

**Tareas:**
- [ ] Testing de flujos de negociación completos
- [ ] Pruebas de concurrencia con múltiples ofertas
- [ ] Validación de notificaciones en tiempo real
- [ ] Testing de performance con gran volumen
- [ ] Pruebas de seguridad y autorización
- [ ] Documentación de casos de uso

---

## Criterios de Aceptación

### Funcionales
- [ ] Compradores pueden crear ofertas con condiciones detalladas
- [ ] Vendedores pueden aceptar, rechazar o hacer contrapropuestas
- [ ] Agentes pueden gestionar ofertas de sus clientes
- [ ] Sistema mantiene historial completo de negociación
- [ ] Notificaciones en tiempo real funcionan correctamente
- [ ] Documentos se pueden adjuntar a ofertas
- [ ] Reportes de ofertas están disponibles

### Técnicos
- [ ] APIs RESTful bien estructuradas y documentadas
- [ ] Validación de datos en backend y frontend
- [ ] Autorización correcta según roles y propiedades
- [ ] Performance: Operaciones de oferta < 1 segundo
- [ ] Notificaciones entregan en < 5 segundos
- [ ] Sistema soporta 100+ ofertas concurrentes

### UX/UI
- [ ] Interfaz intuitiva para crear ofertas
- [ ] Panel claro del estado de negociaciones
- [ ] Notificaciones no intrusivas pero visibles
- [ ] Flujo de negociación fácil de seguir
- [ ] Responsive design en todos los dispositivos

---

## Endpoints de API

### Ofertas
```
GET    /api/offers              # Lista paginada de ofertas del usuario
GET    /api/offers/:id          # Detalles de oferta específica
POST   /api/offers              # Crear nueva oferta
PUT    /api/offers/:id          # Actualizar oferta (solo borrador)
DELETE /api/offers/:id          # Eliminar oferta (solo borrador)
GET    /api/offers/property/:id # Ofertas de una propiedad específica
GET    /api/offers/search       # Búsqueda avanzada de ofertas
```

### Negociación
```
POST   /api/offers/:id/accept   # Aceptar oferta
POST   /api/offers/:id/reject   # Rechazar oferta
POST   /api/offers/:id/counter  # Crear contraoferta
GET    /api/offers/:id/history  # Historial de negociación
POST   /api/offers/:id/withdraw # Retirar oferta (comprador)
```

### Notificaciones
```
GET    /api/offers/:id/notifications    # Notificaciones de una oferta
POST   /api/offers/:id/mark-read       # Marcar notificaciones como leídas
```

### Reportes
```
GET    /api/offers/reports/summary      # Resumen de ofertas
GET    /api/offers/reports/analytics    # Analíticas detalladas
GET    /api/offers/reports/export       # Exportar datos
```

---

## Estados de Oferta y Transiciones

### Estados Principales
```yaml
BORRADOR: 
  descripcion: Oferta en creación, no enviada
  transiciones: [PENDIENTE, ELIMINADA]

PENDIENTE: 
  descripcion: Oferta enviada, esperando respuesta
  transiciones: [ACEPTADA, RECHAZADA, CONTRAOFERTA, RETIRADA, EXPIRADA]

ACEPTADA: 
  descripcion: Oferta aceptada por el vendedor
  transiciones: [FINALIZADA]

RECHAZADA: 
  descripcion: Oferta rechazada definitivamente
  transiciones: []

CONTRAOFERTA: 
  descripcion: Vendedor hizo contrapropuesta
  transiciones: [ACEPTADA, RECHAZADA, CONTRAOFERTA, RETIRADA, EXPIRADA]

RETIRADA: 
  descripcion: Comprador retiró la oferta
  transiciones: []

EXPIRADA: 
  descripcion: Oferta venció sin respuesta
  transiciones: []

FINALIZADA: 
  descripcion: Proceso completado exitosamente
  transiciones: []
```

### Validaciones de Estado
- [ ] Solo ofertas en BORRADOR pueden ser editadas
- [ ] Solo ofertas PENDIENTE o CONTRAOFERTA pueden ser respondidas
- [ ] Solo compradores pueden RETIRAR ofertas
- [ ] Solo vendedores pueden ACEPTAR/RECHAZAR/CONTRAPROPONER
- [ ] Ofertas EXPIRADAS se marcan automáticamente

---

## Flujos de Negociación

### Flujo de Oferta Simple
1. **Comprador** crea oferta en estado BORRADOR
2. Comprador revisa y envía oferta → estado PENDIENTE
3. **Vendedor** recibe notificación de nueva oferta
4. Vendedor puede:
   - Aceptar → estado ACEPTADA → proceso continúa fuera del sistema
   - Rechazar → estado RECHAZADA → fin del proceso
   - Contraproponer → estado CONTRAOFERTA → vuelve al paso 3

### Flujo con Múltiples Rondas
1. Negociación puede tener múltiples rondas de contrapropuestas
2. Sistema mantiene historial completo de todas las ofertas
3. Cada parte puede retirar la oferta en cualquier momento
4. Sistema notifica automáticamente a todas las partes

### Flujo de Expiración
1. Ofertas tienen fecha de vencimiento obligatoria
2. Sistema verifica ofertas expiradas diariamente
3. Ofertas vencidas pasan automáticamente a estado EXPIRADA
4. Se notifica a todas las partes sobre la expiración

---

## Reglas de Negocio

### Validaciones de Oferta
- [ ] Monto debe ser positivo y realista (> 10% del precio lista)
- [ ] Fecha de vencimiento debe ser futura (mín. 24 horas)
- [ ] Fecha de cierre debe ser posterior al vencimiento
- [ ] Usuario debe tener permiso para ofertar en la propiedad
- [ ] Propiedad debe estar disponible para ofertas

### Reglas de Autorización
- [ ] Solo compradores pueden crear ofertas
- [ ] Solo propietarios/agentes autorizados pueden responder
- [ ] Usuarios pueden ver solo sus propias ofertas
- [ ] Administradores pueden ver todas las ofertas
- [ ] Agentes pueden ver ofertas de sus propiedades

### Límites y Restricciones
- [ ] Máximo 3 ofertas activas por usuario por propiedad
- [ ] Ofertas no pueden ser modificadas una vez enviadas
- [ ] Contrapropuestas deben variar al menos 1% del monto anterior
- [ ] Documentos adjuntos máximo 10MB total por oferta

---

## Sistema de Notificaciones

### Eventos de Notificación
```yaml
NUEVA_OFERTA:
  destinatarios: [vendedor, agente]
  canales: [email, push, in-app]
  
OFERTA_ACEPTADA:
  destinatarios: [comprador, agente]
  canales: [email, push, in-app]
  
OFERTA_RECHAZADA:
  destinatarios: [comprador]
  canales: [email, in-app]
  
CONTRAOFERTA:
  destinatarios: [comprador]
  canales: [email, push, in-app]
  
OFERTA_RETIRADA:
  destinatarios: [vendedor, agente]
  canales: [in-app]
  
OFERTA_PROXIMA_VENCER:
  destinatarios: [vendedor, comprador]
  canales: [email, push]
  tiempo: 24 horas antes
  
OFERTA_EXPIRADA:
  destinatarios: [comprador, vendedor]
  canales: [email, in-app]
```

### Templates de Email
- [ ] Nueva oferta recibida
- [ ] Oferta aceptada/rechazada
- [ ] Contraoferta recibida
- [ ] Recordatorio de vencimiento
- [ ] Confirmación de retiro

---

## Reportes y Analíticas

### Métricas de Ofertas
- [ ] **Volumen:** Número de ofertas por período
- [ ] **Conversión:** Porcentaje de ofertas aceptadas
- [ ] **Tiempo promedio:** De oferta a aceptación
- [ ] **Rangos de precio:** Distribución de montos
- [ ] **Actividad por usuario:** Ofertas por comprador/vendedor

### Paneles
- [ ] **Panel Ejecutivo:** KPIs principales
- [ ] **Panel de Agente:** Sus ofertas y propiedades
- [ ] **Panel de Usuario:** Estado de sus ofertas

### Exportaciones
- [ ] Exportar ofertas a Excel/CSV
- [ ] Generar reportes PDF
- [ ] API para integraciones externas

---

## Riesgos y Mitigación

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|-------------|---------|------------|
| Concurrencia en ofertas simultáneas | Media | Alto | Locks optimistas, validaciones de estado |
| Performance con alto volumen | Media | Medio | Indexación DB, caching, paginación |
| Notificaciones perdidas | Baja | Alto | Queue system, retry logic, fallbacks |
| Fraude en ofertas | Baja | Alto | Validaciones estrictas, verificación de usuarios |
| Complejidad UX de negociación | Media | Medio | User testing, wizard flows, tutorials |

---

## Dependencias

### Con Fases Anteriores
- [ ] **Fase 2:** Sistema de autenticación y autorización funcional
- [ ] **Fase 3:** Gestión de usuarios y agentes operativa
- [ ] **Fase 4:** Roles y permisos configurados
- [ ] **Fase 5:** Sistema de propiedades completamente funcional

### Con Sistemas Externos
- [ ] Servicio de notificaciones push configurado
- [ ] Sistema de email transaccional (SendGrid/AWS SES)
- [ ] Socket.io server para tiempo real
- [ ] Almacenamiento para documentos adjuntos

---

## Documentación Entregable

### Técnica
- [ ] API documentation con ejemplos de ofertas
- [ ] Guía de integración con sistema de propiedades
- [ ] Documentación de flujos de negociación
- [ ] Manual de configuración de notificaciones

### Usuario
- [ ] Manual para compradores: Como hacer ofertas
- [ ] Manual para vendedores: Como gestionar ofertas
- [ ] Guía para agentes: Gestión de ofertas de clientes
- [ ] FAQ de problemas comunes en negociación

---

## Métricas de Éxito

| Métrica | Objetivo | Método de Medición |
|---------|----------|-------------------|
| **Tiempo de Respuesta** | < 1 segundo | Performance monitoring |
| **Tasa de Conversión de Ofertas** | > 15% | Analíticas de ofertas aceptadas |
| **Satisfacción de Usuario** | > 4.2/5 estrellas | User feedback surveys |
| **Uptime del Sistema** | > 99.5% | Monitoring de servicios |
| **Tiempo de Negociación** | < 5 días promedio | Analíticas de duración |

---

## Documentación Relacionada

### Documentos de Gestión de la Fase 6
- [Análisis de Riesgos - Fase 6](./fase-06-analisis-riesgos.md)
- [Plan de Migración y Validación de Datos - Fase 6](./fase-06-plan-migracion-datos.md)
- [Checklist de Pruebas - Fase 6](./fase-06-checklist-pruebas.md)
- [Procedimientos de Rollback - Fase 6](./fase-06-procedimientos-rollback.md)

### Documentos de Seguimiento y Control
- [Métricas y KPIs - Fase 6](./fase-06-metricas-kpi.md)
- [Registro de Incidentes - Fase 6](./fase-06-registro-incidentes.md)
- [Plan de Comunicación con Stakeholders - Fase 6](./fase-06-plan-comunicacion-stakeholders.md)
- [Validación de Integración entre Módulos - Fase 6](./fase-06-validacion-integracion.md)

### Documentos de Capacitación y Cierre
- [Manual de Capacitación - Fase 6](./fase-06-manual-capacitacion.md)
- [Reporte Final de Fase 6](./fase-06-reporte-final.md)

---

**Fecha de Creación:** 20/11/2025
**Última Actualización:** 20/11/2025
**Versión:** 1.0