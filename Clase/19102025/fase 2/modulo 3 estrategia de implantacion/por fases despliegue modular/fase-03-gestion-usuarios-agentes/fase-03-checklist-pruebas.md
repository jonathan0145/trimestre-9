# Checklist de Pruebas - Fase 3: Gestión de Usuarios y Agentes

## Información de la Fase

**Nombre de la Fase:** Gestión de Usuarios y Agentes
**Número de Fase:** 03
**Fecha de Pruebas:** 19/01/2026 - 21/01/2026
**Responsable QA:** Carlos Vega - QA Analyst
**Responsable Técnico:** Carmen López - Backend Lead

---

## Checklist de Preparación

### Entorno de Pruebas
- [ ] Entorno de desarrollo configurado con módulos de Fase 1 y 2
- [ ] Base de datos de pruebas con 500+ usuarios semilla de diferentes roles
- [ ] Variables de entorno configuradas (TOKEN_AUTH, RUTA_SUBIDA, CONFIG_CACHE)
- [ ] Servicios de almacenamiento simulados para imágenes de perfil
- [ ] Registros habilitados para depuración de gestión de usuario
- [ ] Herramientas de prueba instaladas (Jest, Supertest, Cypress, Artillery)

### Datos de Prueba
- [ ] 150 usuarios compradores con perfiles básicos
- [ ] 200 usuarios vendedores con información de propiedades
- [ ] 100 agentes con perfiles profesionales completos
- [ ] 50 administradores con permisos completos
- [ ] Escenarios de prueba documentados (347 casos de prueba)
- [ ] Datos de casos límite (caracteres especiales, campos extremos)

---

## Pruebas Funcionales - Backend

### Endpoints de Gestión de Usuarios

#### USER-001: GET /api/users (Lista de usuarios con paginación)
- [ ] **Caso exitoso:** Lista paginada con límite/desplazamiento, respuesta 200
- [ ] **Paginación:** Diferentes tamaños de página (10, 25, 50, 100)
- [ ] **Filtros:** Por rol (comprador, vendedor, agente, admin)
- [ ] **Filtros:** Por estado (activo, inactivo, pendiente)
- [ ] **Ordenamiento:** Por nombre, fecha de registro, último inicio de sesión
- [ ] **Búsqueda:** Por nombre, email (parcial y exacta)
- [ ] **Autorización:** Solo admin y agentes pueden ver lista completa
- [ ] **Campos sensitivos:** Contraseñas y tokens no se exponen

**Resultado:** ⏳ Pendiente
**Notas:** 

#### USER-002: GET /api/users/:id (Detalles de usuario específico)
- [ ] **Caso exitoso:** Usuario existente, respuesta 200 con datos completos
- [ ] **Usuario inexistente:** Error 404 con mensaje apropiado
- [ ] **Autorización:** Users solo ven su propio perfil
- [ ] **Autorización:** Admins ven cualquier perfil
- [ ] **Autorización:** Agentes ven perfiles de sus clientes
- [ ] **Campos sensitivos:** Datos sensibles filtrados según rol
- [ ] **Relaciones:** Profile data incluida si existe
- [ ] **Performance:** Respuesta < 500ms para cualquier usuario

**Resultado:** ⏳ Pendiente
**Notas:**

#### USER-003: POST /api/users (Crear nuevo usuario - Solo Admin)
- [ ] **Caso exitoso:** Datos válidos, usuario creado, respuesta 201
- [ ] **Validación campos:** firstName, lastName, email, role requeridos
- [ ] **Email único:** Error 409 si email ya existe
- [ ] **Validación email:** Formato válido requerido
- [ ] **Validación rol:** Solo roles válidos aceptados
- [ ] **Password temporal:** Generada automáticamente y enviada por email
- [ ] **Autorización:** Solo administradores pueden crear usuarios
- [ ] **Audit log:** Creación registrada en logs de auditoría

**Resultado:** ⏳ Pendiente
**Notas:**

#### USER-004: PUT /api/users/:id (Actualizar usuario)
- [ ] **Caso exitoso:** Datos válidos, usuario actualizado, respuesta 200
- [ ] **Autorización propia:** Usuario puede editar su propio perfil
- [ ] **Autorización admin:** Admin puede editar cualquier usuario
- [ ] **Campos protegidos:** Email requiere verificación adicional
- [ ] **Campos protegidos:** Role solo modificable por admin
- [ ] **Validaciones:** Todos los campos validados correctamente
- [ ] **History tracking:** Cambios registrados en audit log
- [ ] **Notificación:** Usuario notificado de cambios importantes

**Resultado:** ⏳ Pendiente
**Notas:**

#### USER-005: DELETE /api/users/:id (Eliminar/desactivar usuario)
- [ ] **Soft delete:** Usuario marcado como inactive, no eliminado físicamente
- [ ] **Autorización:** Solo administradores pueden desactivar usuarios
- [ ] **Data integrity:** Relaciones preservadas (propiedades, ofertas)
- [ ] **Cascade effects:** Sessions invalidadas, tokens revocados
- [ ] **Restore capability:** Posibilidad de reactivar usuario
- [ ] **Audit trail:** Eliminación registrada con justificación
- [ ] **Notificación:** Usuario notificado de desactivación
- [ ] **Admin protection:** No se puede eliminar último admin

**Resultado:** ⏳ Pendiente
**Notas:**

### Endpoints de Búsqueda y Filtrado

#### SEARCH-001: GET /api/users/search (Búsqueda avanzada)
- [ ] **Búsqueda por nombre:** Partial matching, case insensitive
- [ ] **Búsqueda por email:** Partial matching con validación
- [ ] **Filtros combinados:** Rol + status + fecha de registro
- [ ] **Filtros geográficos:** Por ciudad, estado (si aplicable)
- [ ] **Ordenamiento múltiple:** Por varios campos simultáneamente
- [ ] **Performance:** < 1 segundo con 10,000+ usuarios
- [ ] **Paginación:** Funciona correctamente con resultados filtrados
- [ ] **Autorización:** Resultados filtrados según permisos del usuario

**Resultado:** ⏳ Pendiente
**Notas:**

#### SEARCH-002: GET /api/users/search/agents (Búsqueda específica de agentes)
- [ ] **Filtros profesionales:** Por especialización, experiencia
- [ ] **Filtros geográficos:** Por área de servicio, ubicación
- [ ] **Filtros de rating:** Por calificación mínima
- [ ] **Filtros de verificación:** Solo agentes verificados
- [ ] **Información adicional:** License info, agency data incluida
- [ ] **Performance:** Optimizada para búsquedas frecuentes
- [ ] **Cache:** Resultados populares cacheados
- [ ] **Analytics:** Búsquedas registradas para analytics

**Resultado:** ⏳ Pendiente
**Notas:**

### Endpoints de Perfiles

#### PROFILE-001: GET /api/profile (Perfil del usuario autenticado)
- [ ] **Caso exitoso:** Perfil completo del usuario actual
- [ ] **Datos básicos:** Información personal y contacto
- [ ] **Datos profesionales:** Info específica del rol (si agent)
- [ ] **Configuraciones:** Preferencias y settings del usuario
- [ ] **Estadísticas:** Metrics relevantes según rol
- [ ] **Avatar:** URL de imagen de perfil si existe
- [ ] **Privacy settings:** Configuración de privacidad aplicada
- [ ] **Last updated:** Timestamp de última modificación

**Resultado:** ⏳ Pendiente
**Notas:**

#### PROFILE-002: PUT /api/profile (Actualizar perfil propio)
- [ ] **Información básica:** Nombre, teléfono, dirección
- [ ] **Validaciones:** Todos los campos validados apropiadamente
- [ ] **Upload de avatar:** Imagen de perfil funciona correctamente
- [ ] **Campos específicos rol:** Agent license, specializations
- [ ] **Privacy settings:** Usuario puede controlar visibilidad
- [ ] **Email change:** Requiere verificación si se cambia email
- [ ] **Password change:** Requiere confirmación de password actual
- [ ] **Optimistic updates:** Frontend actualiza inmediatamente

**Resultado:** ⏳ Pendiente
**Notas:**

#### PROFILE-003: POST /api/profile/avatar (Upload de imagen de perfil)
- [ ] **Formatos soportados:** JPG, PNG, WebP
- [ ] **Tamaño máximo:** 5MB limit enforced
- [ ] **Validación de imagen:** Archivo realmente es imagen
- [ ] **Resize automático:** Imágenes redimensionadas apropiadamente
- [ ] **CDN upload:** Imagen almacenada en storage service
- [ ] **URL generation:** URL pública generada correctamente
- [ ] **Security:** Prevención de malicious file upload
- [ ] **Cleanup:** Imagen anterior eliminada al subir nueva

**Resultado:** ⏳ Pendiente
**Notas:**

### Endpoints Específicos de Agentes

#### AGENT-001: GET /api/agents (Lista de agentes públicos)
- [ ] **Solo agentes:** Solo usuarios con rol agent
- [ ] **Solo activos:** Agentes activos y verificados
- [ ] **Información pública:** Solo datos que pueden ser públicos
- [ ] **Filtros:** Por especialización, área de servicio
- [ ] **Ordenamiento:** Por rating, experiencia, reviews
- [ ] **Paginación:** Funciona correctamente
- [ ] **Performance:** Optimizado para búsquedas públicas
- [ ] **Cache:** Results cacheados por tiempo apropiado

**Resultado:** ⏳ Pendiente
**Notas:**

#### AGENT-002: GET /api/agents/:id/public (Perfil público de agente)
- [ ] **Información profesional:** License, agency, specializations
- [ ] **Estadísticas:** Sales, ratings, reviews (si público)
- [ ] **Contacto:** Información de contacto profesional
- [ ] **Verificaciones:** Badges de verificación mostrados
- [ ] **Privacy compliant:** Solo info marcada como pública
- [ ] **Performance:** Response time < 300ms
- [ ] **Analytics:** Vistas de perfil registradas
- [ ] **Contact tracking:** Tracking de leads generados

**Resultado:** ⏳ Pendiente
**Notas:**

#### AGENT-003: PUT /api/agents/:id/professional (Actualizar info profesional)
- [ ] **Autorización:** Solo el agente puede editar su info
- [ ] **License info:** Número de licencia, expiración
- [ ] **Specializations:** Lista de especializaciones
- [ ] **Service areas:** Áreas geográficas de servicio
- [ ] **Agency info:** Información de la agencia
- [ ] **Validaciones:** Todos los campos profesionales validados
- [ ] **Verification reset:** Campos críticos requieren re-verificación
- [ ] **Audit trail:** Cambios registrados para compliance

**Resultado:** ⏳ Pendiente
**Notas:**

### Middleware y Autorización

#### AUTH-001: authMiddleware (Verificación de autenticación)
- [ ] **Token válido:** Permite acceso con JWT válido
- [ ] **Token inválido:** Bloquea acceso con token malformado
- [ ] **Token expirado:** Bloquea acceso con token vencido
- [ ] **Sin token:** Error 401 cuando Authorization header ausente
- [ ] **User population:** req.user poblado correctamente
- [ ] **Role information:** Información de rol disponible

**Resultado:** ⏳ Pendiente
**Notas:**

#### AUTH-002: roleMiddleware (Verificación de roles)
- [ ] **Admin access:** Admin puede acceder a todo
- [ ] **Agent restrictions:** Agentes solo ven datos apropiados
- [ ] **User restrictions:** Users solo ven sus propios datos
- [ ] **Multiple roles:** Manejo correcto de usuarios con múltiples roles
- [ ] **Error messages:** Mensajes claros para acceso denegado
- [ ] **Logging:** Accesos denegados registrados apropiadamente

**Resultado:** ⏳ Pendiente
**Notas:**

### Base de Datos
- [ ] **CRUD Operations:** Create, Read, Update, Delete usuarios funcionan
- [ ] **Relaciones:** User-Profile, User-Role relaciones mantenidas
- [ ] **Índices:** Queries optimizadas con índices apropiados
- [ ] **Constraints:** Constraints de DB funcionando (unique email)
- [ ] **Transactions:** Operaciones complejas en transacciones
- [ ] **Performance:** Queries < 100ms para operaciones comunes

**Resultado:** ⏳ Pendiente
**Notas:**

---

## Pruebas Funcionales - Frontend

### Páginas de Gestión de Usuarios

#### PAGE-001: UsersPage (Lista de usuarios)
- [ ] **Carga inicial:** Página carga correctamente con lista de usuarios
- [ ] **Paginación:** Navegación entre páginas funciona
- [ ] **Búsqueda:** Search bar filtra resultados en tiempo real
- [ ] **Filtros:** Filtros por rol y status funcionan
- [ ] **Ordenamiento:** Columnas ordenables funcionan
- [ ] **Responsive:** Página funciona en móvil y tablet
- [ ] **Performance:** Página carga < 2 segundos
- [ ] **Error handling:** Errores mostrados apropiadamente

**Resultado:** ⏳ Pendiente
**Notas:**

#### PAGE-002: UserDetailsPage (Detalles de usuario)
- [ ] **Información completa:** Todos los datos del usuario mostrados
- [ ] **Edición in-place:** Campos editables funcionan correctamente
- [ ] **Avatar:** Imagen de perfil se muestra y actualiza
- [ ] **Role-specific data:** Información específica del rol mostrada
- [ ] **Permissions:** Solo campos editables según permisos
- [ ] **History:** Historial de cambios visible (si admin)
- [ ] **Navigation:** Navegación entre usuarios fluida
- [ ] **Error states:** Usuarios inexistentes manejados

**Resultado:** ⏳ Pendiente
**Notas:**

#### PAGE-003: AgentsPage (Gestión de agentes)
- [ ] **Lista especializada:** Solo agentes mostrados
- [ ] **Información profesional:** License, agency, ratings visibles
- [ ] **Filtros específicos:** Por especialización, área, rating
- [ ] **Verificación visual:** Badges de verificación mostrados
- [ ] **Contacto:** Información de contacto disponible
- [ ] **Estadísticas:** Performance metrics visibles
- [ ] **Export:** Funcionalidad de exportar lista de agentes
- [ ] **Bulk actions:** Acciones masivas funcionan

**Resultado:** ⏳ Pendiente
**Notas:**

### Componentes de Usuario

#### COMP-001: UserCard (Tarjeta de usuario)
- [ ] **Información básica:** Nombre, rol, avatar mostrados
- [ ] **Status visual:** Estado del usuario (active/inactive)
- [ ] **Quick actions:** Botones de acción rápida funcionan
- [ ] **Role indicators:** Rol claramente identificable
- [ ] **Contact info:** Email y teléfono (si permitido)
- [ ] **Responsive:** Funciona en diferentes screen sizes
- [ ] **Loading states:** Loading skeletons mientras carga data
- [ ] **Error states:** Manejo de errores de carga

**Resultado:** ⏳ Pendiente
**Notas:**

#### COMP-002: AgentCard (Tarjeta de agente)
- [ ] **Info profesional:** License, agency mostrados
- [ ] **Specializations:** Tags de especialización
- [ ] **Rating:** Estrellas o score de calificación
- [ ] **Verification badge:** Badge de verificación visible
- [ ] **Contact button:** Botón de contacto funcional
- [ ] **Service areas:** Áreas de servicio listadas
- [ ] **Hover states:** Interacciones hover apropiadas
- [ ] **Click handlers:** Navigate to profile funciona

**Resultado:** ⏳ Pendiente
**Notas:**

#### COMP-003: UserSearchBar (Búsqueda de usuarios)
- [ ] **Search real-time:** Búsqueda mientras el usuario tipea
- [ ] **Autocomplete:** Sugerencias de búsqueda aparecen
- [ ] **Filters integration:** Integra con filtros de la página
- [ ] **Clear functionality:** Botón clear funciona
- [ ] **Keyboard navigation:** Arrow keys y Enter funcionan
- [ ] **Performance:** No lag durante typing
- [ ] **Mobile friendly:** Funciona bien en móvil
- [ ] **Error handling:** Errores de búsqueda manejados

**Resultado:** ⏳ Pendiente
**Notas:**

### Estado Global (Redux)

#### STATE-001: usersSlice (Estado de usuarios)
- [ ] **Carga inicial:** Users loaded from API
- [ ] **CRUD operations:** Create, Update, Delete reflejan en state
- [ ] **Filtering:** State filtering funciona correctamente
- [ ] **Pagination:** Pagination state management
- [ ] **Loading states:** Loading indicators apropiados
- [ ] **Error handling:** Errors stored y displayed apropiadamente
- [ ] **Optimistic updates:** UI updates before API confirmation
- [ ] **Cache management:** Stale data refreshed apropiadamente

**Resultado:** ⏳ Pendiente
**Notas:**

#### STATE-002: profileSlice (Estado del perfil)
- [ ] **Profile loading:** Profile loaded on app init
- [ ] **Profile updates:** Updates reflejados inmediatamente
- [ ] **Avatar changes:** Avatar updates in real time
- [ ] **Settings:** User settings persisted correctamente
- [ ] **Privacy:** Privacy settings applied everywhere
- [ ] **Validation:** Client-side validation state
- [ ] **Dirty state:** Unsaved changes tracking
- [ ] **Auto-save:** Optional auto-save functionality

**Resultado:** ⏳ Pendiente
**Notas:**

---

## Pruebas de Integración

### Integración Backend-Frontend
- [ ] **API calls:** Todos los API calls funcionan end-to-end
- [ ] **Authentication:** JWT tokens passed y validated correctamente
- [ ] **Error propagation:** API errors mostrados en UI
- [ ] **Loading states:** Loading indicators durante API calls
- [ ] **Data consistency:** Data consistent entre backend y frontend
- [ ] **File uploads:** Avatar upload funciona end-to-end
- [ ] **Real-time updates:** Changes reflejados en tiempo real
- [ ] **Offline handling:** Graceful handling cuando API down

**Resultado:** ⏳ Pendiente
**Notas:**

### Integración con Fases Anteriores
- [ ] **Authentication (Fase 2):** Login/logout funciona perfectamente
- [ ] **Permissions (Fase 2):** Role-based permissions aplicadas
- [ ] **Database (Fase 1):** User model compatible con nuevas features
- [ ] **API consistency:** APIs consistent con previous phases
- [ ] **Shared components:** Componentes compartidos funcionan
- [ ] **Navigation:** Navigation flows entre phases
- [ ] **Data migration:** Existing users work with new features
- [ ] **Backward compatibility:** Previous functionality no broken

**Resultado:** ⏳ Pendiente
**Notas:**

---

## Pruebas de Performance

### Performance Backend
- [ ] **User listing:** < 500ms para 10,000+ usuarios
- [ ] **Search queries:** < 1 segundo para búsquedas complejas
- [ ] **Profile loading:** < 300ms para cualquier perfil
- [ ] **Concurrent users:** 1,000+ usuarios simultáneos
- [ ] **Database queries:** Todas las queries optimizadas
- [ ] **Memory usage:** < 70% memory utilization
- [ ] **CPU usage:** < 60% CPU bajo load normal
- [ ] **API throughput:** > 1,000 requests/second

**Resultado:** ⏳ Pendiente
**Notas:**

### Performance Frontend
- [ ] **Page load time:** < 2 segundos initial load
- [ ] **Search responsiveness:** < 300ms search response
- [ ] **Navigation:** < 500ms between pages
- [ ] **Image loading:** Avatars load < 1 segundo
- [ ] **Bundle size:** JavaScript bundle optimizado
- [ ] **Memory leaks:** No memory leaks detectados
- [ ] **Mobile performance:** Good performance en mobile
- [ ] **Network resilience:** Funciona bien con slow networks

**Resultado:** ⏳ Pendiente
**Notas:**

---

## Pruebas de Seguridad

### Authorization Tests
- [ ] **Role boundaries:** Users can't access unauthorized data
- [ ] **Data isolation:** Users only see their own data
- [ ] **Admin privileges:** Admins have appropriate access
- [ ] **Agent restrictions:** Agents see only permitted data
- [ ] **Field-level security:** Sensitive fields protected
- [ ] **API security:** All endpoints properly protected
- [ ] **CSRF protection:** Cross-site request forgery prevented
- [ ] **Input validation:** All inputs validated and sanitized

**Resultado:** ⏳ Pendiente
**Notas:**

### Privacy Tests
- [ ] **Data encryption:** Sensitive data encrypted at rest
- [ ] **PII protection:** Personal info properly protected
- [ ] **Data minimization:** Only necessary data collected/shown
- [ ] **Consent management:** User consent tracked appropriately
- [ ] **Right to deletion:** User data can be deleted
- [ ] **Data portability:** User data can be exported
- [ ] **Audit trails:** All access logged appropriately
- [ ] **Compliance:** GDPR/privacy compliance validated

**Resultado:** ⏳ Pendiente
**Notas:**

---

## Pruebas de Usabilidad

### User Experience Tests
- [ ] **Intuitive navigation:** Users can navigate without training
- [ ] **Clear labeling:** All buttons and fields clearly labeled
- [ ] **Consistent design:** UI consistent across all pages
- [ ] **Error messages:** Clear and helpful error messages
- [ ] **Help and guidance:** Contextual help available
- [ ] **Accessibility:** Meets WCAG 2.1 AA standards
- [ ] **Mobile experience:** Good UX on mobile devices
- [ ] **Loading indicators:** Clear feedback during operations

**Resultado:** ⏳ Pendiente
**Notas:**

### Agent-Specific UX Tests
- [ ] **Professional profile:** Easy to complete and maintain
- [ ] **Service areas:** Easy to define and update
- [ ] **Specializations:** Simple to select and modify
- [ ] **Verification process:** Clear process for verification
- [ ] **Client management:** Easy to view and manage clients
- [ ] **Statistics view:** Meaningful metrics displayed
- [ ] **Contact management:** Efficient contact workflows
- [ ] **Mobile optimization:** Great mobile experience for field work

**Resultado:** ⏳ Pendiente
**Notas:**

---

## Resumen de Testing

### Estadísticas de Pruebas
- **Total Test Cases:** 347
- **Backend Tests:** 89
- **Frontend Tests:** 156
- **Integration Tests:** 67
- **Performance Tests:** 16
- **Security Tests:** 19

### Criterios de Aceptación
- [ ] **Pass Rate:** > 95% de todas las pruebas
- [ ] **Critical Issues:** 0 bugs críticos
- [ ] **Performance:** Todas las métricas de performance cumplidas
- [ ] **Security:** No vulnerabilidades de seguridad high/critical
- [ ] **User Acceptance:** > 4.0/5 en user testing
- [ ] **Accessibility:** WCAG 2.1 AA compliance verificado
- [ ] **Cross-browser:** Funciona en Chrome, Firefox, Safari, Edge
- [ ] **Mobile:** Funcionalidad completa en iOS y Android

### Sign-offs Requeridos
- [ ] **QA Lead:** Carlos Vega - Quality approval
- [ ] **Tech Lead:** Carmen López - Technical approval  
- [ ] **UX Lead:** David Chen - User experience approval
- [ ] **Security:** Miguel Torres - Security clearance
- [ ] **Product Owner:** Project Manager - Business approval

---

**RESULTADO FINAL: ⏳ TESTING EN PROGRESO**

**Notas Finales:**
Testing comenzará el 19/01/2026. Resultados esperados para 21/01/2026.
Criterios de aceptación deben cumplirse 100% antes de deployment.

**Responsable de Seguimiento:** Carlos Vega - QA Analyst  
**Próxima Revisión:** Daily durante testing period  
**Aprobación Final:** 21/01/2026