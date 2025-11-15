# Template - Checklist de Pruebas por Fase

## Información de la Fase

**Nombre de la Fase:** [NOMBRE_FASE]
**Número de Fase:** [NUMERO]
**Fecha de Pruebas:** [FECHA_INICIO] - [FECHA_FIN]
**Responsable QA:** [RESPONSABLE_QA]
**Responsable Técnico:** [RESPONSABLE_TECNICO]

---

## Checklist de Preparación

### Entorno de Pruebas
- [ ] Entorno de desarrollo configurado y funcional
- [ ] Base de datos de pruebas con datos semilla
- [ ] Variables de entorno configuradas correctamente
- [ ] Servicios externos mockados o configurados
- [ ] Logs habilitados para debugging
- [ ] Herramientas de prueba instaladas (Postman, Jest, Cypress, etc.)

### Datos de Prueba
- [ ] Usuarios de prueba creados para cada rol
- [ ] Datos de ejemplo cargados en BD
- [ ] Archivos de prueba preparados (si aplica)
- [ ] Escenarios de prueba documentados
- [ ] Casos edge documentados

---

## Pruebas Funcionales - Backend

### Endpoints y APIs

#### [ENDPOINT_1]: [METODO] /api/[ruta]
- [ ] **Caso exitoso:** Datos válidos, respuesta 200/201
- [ ] **Validación de entrada:** Campos requeridos
- [ ] **Validación de tipos:** Tipos de datos incorrectos
- [ ] **Autorización:** Sin token, token inválido, permisos insuficientes
- [ ] **Casos edge:** Límites, valores extremos
- [ ] **Manejo de errores:** Respuestas 400, 401, 403, 404, 500

**Resultado:** ✅ Aprobado / ❌ Fallido / ⏸️ Pendiente
**Notas:** [Observaciones]

#### [ENDPOINT_2]: [METODO] /api/[ruta]
- [ ] **Caso exitoso:** Datos válidos, respuesta 200/201
- [ ] **Validación de entrada:** Campos requeridos
- [ ] **Validación de tipos:** Tipos de datos incorrectos
- [ ] **Autorización:** Sin token, token inválido, permisos insuficientes
- [ ] **Casos edge:** Límites, valores extremos
- [ ] **Manejo de errores:** Respuestas 400, 401, 403, 404, 500

**Resultado:** ✅ Aprobado / ❌ Fallido / ⏸️ Pendiente
**Notas:** [Observaciones]

### Base de Datos
- [ ] **CRUD Operations:** Create, Read, Update, Delete funcionan
- [ ] **Integridad referencial:** Foreign keys funcionan
- [ ] **Validaciones:** Constraints de BD respetadas
- [ ] **Transacciones:** Rollback en caso de error
- [ ] **Performance:** Queries optimizadas, índices efectivos

### Integraciones
- [ ] **APIs externas:** Servicios de terceros funcionan
- [ ] **WebSockets:** Conexiones en tiempo real estables
- [ ] **File uploads:** Subida y descarga de archivos
- [ ] **Email services:** Envío de correos electrónicos
- [ ] **Push notifications:** Notificaciones móviles

---

## Pruebas Funcionales - Frontend

### Componentes de UI

#### [COMPONENTE_1]: [NombreComponente]
- [ ] **Renderizado:** Se muestra correctamente
- [ ] **Props:** Recibe y maneja props correctamente
- [ ] **Estado:** State management funciona
- [ ] **Eventos:** Click, submit, change funcionan
- [ ] **Validaciones:** Formularios validados
- [ ] **Responsive:** Se adapta a diferentes tamaños

**Resultado:** ✅ Aprobado / ❌ Fallido / ⏸️ Pendiente
**Notas:** [Observaciones]

#### [COMPONENTE_2]: [NombreComponente]
- [ ] **Renderizado:** Se muestra correctamente
- [ ] **Props:** Recibe y maneja props correctamente
- [ ] **Estado:** State management funciona
- [ ] **Eventos:** Click, submit, change funcionan
- [ ] **Validaciones:** Formularios validados
- [ ] **Responsive:** Se adapta a diferentes tamaños

**Resultado:** ✅ Aprobado / ❌ Fallido / ⏸️ Pendiente
**Notas:** [Observaciones]

### Páginas/Vistas

#### [PAGINA_1]: [NombrePagina]
- [ ] **Navegación:** URL correcta, breadcrumbs
- [ ] **Carga:** Página carga completamente
- [ ] **Contenido:** Información mostrada correctamente
- [ ] **Interacciones:** Botones, links, formularios funcionan
- [ ] **Estados:** Loading, error, success manejados
- [ ] **Permisos:** Solo usuarios autorizados acceden

**Resultado:** ✅ Aprobado / ❌ Fallido / ⏸️ Pendiente
**Notas:** [Observaciones]

### Flujos de Usuario

#### Flujo: [NOMBRE_FLUJO]
**Pasos:**
1. [ ] [Paso 1 del flujo]
2. [ ] [Paso 2 del flujo]
3. [ ] [Paso 3 del flujo]
4. [ ] [Paso 4 del flujo]

**Resultado:** ✅ Aprobado / ❌ Fallido / ⏸️ Pendiente
**Criterio:** [Usuario puede completar el flujo exitosamente]

---

## Pruebas de Integración

### Frontend ↔ Backend
- [ ] **Autenticación:** Login/logout funcionan
- [ ] **APIs:** Llamadas a endpoints exitosas
- [ ] **Estado global:** Redux/Context actualizado
- [ ] **Error handling:** Errores de API manejados
- [ ] **Loading states:** Indicadores de carga mostrados

### Módulo ↔ Módulos Previos
- [ ] **Dependencias:** Módulos anteriores no afectados
- [ ] **Datos compartidos:** Información consistente
- [ ] **Navegación:** Links entre módulos funcionan
- [ ] **Permisos:** Roles y permisos integrados
- [ ] **Performance:** No degradación en módulos existentes

---

## Pruebas Técnicas

### Performance
- [ ] **Response time:** APIs < [X]ms promedio
- [ ] **Page load:** Páginas cargan < [X]s
- [ ] **Bundle size:** JS bundle < [X]MB
- [ ] **Memory usage:** Sin memory leaks detectados
- [ ] **Database:** Queries optimizadas < [X]ms

### Seguridad
- [ ] **Autenticación:** Tokens validados correctamente
- [ ] **Autorización:** Roles y permisos respetados
- [ ] **Input validation:** Sanitización de entradas
- [ ] **XSS Protection:** Sin vulnerabilidades XSS
- [ ] **SQL Injection:** Queries parametrizadas
- [ ] **HTTPS:** Conexiones seguras
- [ ] **Secrets:** Variables sensibles no expuestas

### Usabilidad
- [ ] **Intuitive navigation:** Navegación clara
- [ ] **Error messages:** Mensajes descriptivos
- [ ] **Loading feedback:** Indicadores apropiados
- [ ] **Accessibility:** Standards WCAG cumplidos
- [ ] **Mobile friendly:** Funciona en dispositivos móviles

---

## Pruebas Automatizadas

### Unit Tests
- [ ] **Backend tests:** `npm test` en backend pasa
- [ ] **Frontend tests:** `npm test` en frontend pasa
- [ ] **Coverage:** > 80% líneas cubiertas
- [ ] **Edge cases:** Casos límite cubiertos

### Integration Tests
- [ ] **API tests:** Postman/Newman collection pasa
- [ ] **E2E tests:** Cypress tests pasan
- [ ] **Database tests:** Migraciones y seeders funcionan

---

## Criterios de Aceptación Final

### Must Have (Obligatorios)
- [ ] Todas las funcionalidades core implementadas
- [ ] Sin errores críticos o bloqueantes
- [ ] Performance dentro de límites aceptables
- [ ] Seguridad validada sin vulnerabilidades altas
- [ ] Integración con fases previas exitosa

### Should Have (Deseables)
- [ ] Todos los casos de prueba pasados
- [ ] Cobertura de tests > 80%
- [ ] UX/UI validada por usuarios
- [ ] Documentación actualizada

### Nice to Have (Opcionales)
- [ ] Performance optimizada
- [ ] Tests adicionales automatizados
- [ ] Mejoras de usabilidad implementadas

---

## Resultados y Reporte

### Resumen Ejecutivo
**Estado General:** ✅ Aprobado / ❌ Requiere Ajustes / ⏸️ Pendiente

**Estadísticas:**
- **Total casos de prueba:** [X]
- **Casos pasados:** [X] ([X]%)
- **Casos fallidos:** [X] ([X]%)
- **Casos pendientes:** [X] ([X]%)

### Incidencias Encontradas
**Críticas:** [X] (Deben resolverse antes de avanzar)
**Altas:** [X] (Resolver en esta fase)
**Medias:** [X] (Resolver según prioridad)
**Bajas:** [X] (Pueden diferirse)

### Recomendaciones
1. [Recomendación 1]
2. [Recomendación 2]
3. [Recomendación 3]

### Aprobación para Siguiente Fase
- [ ] **Todas las pruebas críticas pasadas**
- [ ] **Incidencias críticas resueltas**
- [ ] **Performance dentro de límites**
- [ ] **Integración validada**
- [ ] **Documentación actualizada**

**Aprobado por:**
- [ ] **QA Lead:** [NOMBRE] - [FECHA]
- [ ] **Tech Lead:** [NOMBRE] - [FECHA]
- [ ] **Project Manager:** [NOMBRE] - [FECHA]

---

**Ejecutado por:** [RESPONSABLE_EJECUCION]
**Fecha de Ejecución:** [FECHA_EJECUCION]
**Tiempo Total de Pruebas:** [X] horas