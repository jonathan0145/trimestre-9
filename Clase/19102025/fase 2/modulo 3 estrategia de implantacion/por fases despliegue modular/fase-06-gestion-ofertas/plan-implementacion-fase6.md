# Plan de Implementación - Fase 6: Gestión de Ofertas

## Información de la Fase

**Nombre de la Fase:** Gestión de Ofertas
**Número de Fase:** 6
**Fecha de Inicio:** 05/02/2026
**Fecha de Fin:** 11/02/2026
**Responsable Principal:** Carmen López (Desarrollador Backend)

---

## Objetivos de la Fase

### Objetivo Principal
Implementar sistema completo de ofertas y negociación entre compradores, vendedores y agentes.

### Objetivos Específicos
- [ ] Desarrollar flujo completo de ofertas
- [ ] Implementar sistema de negociación
- [ ] Crear notificaciones automáticas
- [ ] Integrar con sistema de propiedades
- [ ] Implementar validaciones de negocio
- [ ] Crear reportes de ofertas

---

## Componentes Principales

### Backend
- [ ] `offerController.js`: Gestión de ofertas y negociación
- [ ] `offerService.js`: Lógica de negocio de ofertas
- [ ] `negotiationService.js`: Proceso de negociación
- [ ] `offerValidationService.js`: Validaciones de ofertas

### Frontend
- [ ] `OffersPage.js`: Dashboard de ofertas
- [ ] `MakeOfferModal.js`: Formulario para hacer ofertas
- [ ] `OfferDetailsPage.js`: Detalles de oferta específica
- [ ] `NegotiationHistory.js`: Historial de negociación
- [ ] `OfferStatusCard.js`: Estado visual de ofertas

### Funcionalidades Clave
- ✅ **Crear Ofertas:** Formulario con validaciones
- ✅ **Contrapropuestas:** Sistema de negociación iterativa
- ✅ **Estados de Oferta:** Pendiente, aceptada, rechazada, expirada
- ✅ **Notificaciones:** Alerts automáticas por cambios de estado
- ✅ **Historial:** Tracking completo de negociación
- ✅ **Validaciones:** Ofertas válidas según reglas de negocio

---

## Endpoints de API

```
GET    /api/offers              # Lista de ofertas del usuario
GET    /api/offers/:id          # Detalles de oferta específica
POST   /api/offers              # Crear nueva oferta
PUT    /api/offers/:id          # Actualizar/Contrapropuesta
POST   /api/offers/:id/accept   # Aceptar oferta
POST   /api/offers/:id/reject   # Rechazar oferta
GET    /api/offers/property/:id # Ofertas de una propiedad
```

---

## Criterios de Aceptación

### Funcionales
- [ ] Compradores pueden hacer ofertas en propiedades
- [ ] Vendedores pueden aceptar/rechazar/contraproponer
- [ ] Agentes pueden gestionar ofertas de sus clientes
- [ ] Sistema notifica cambios de estado automáticamente
- [ ] Historial de negociación se mantiene completo

### Técnicos
- [ ] Validaciones previenen ofertas inválidas
- [ ] Estados de oferta se actualizan consistentemente
- [ ] Notificaciones se envían en tiempo real
- [ ] Performance adecuada con múltiples ofertas

---

## Estados de Oferta

```
1. PENDIENTE: Oferta enviada, esperando respuesta
2. ACEPTADA: Oferta aceptada por el vendedor
3. RECHAZADA: Oferta rechazada
4. CONTRAOFERTA: Vendedor hizo contrapropuesta
5. EXPIRADA: Oferta venció sin respuesta
6. CANCELADA: Comprador canceló la oferta
```

---

**Fecha de Creación:** 12/11/2025
**Versión:** 1.0