# Piloto: Implementación en Área Pequeña

## Proyecto: Inmotech

### Objetivo
Realizar una implementación inicial del sistema Inmotech en un área controlada para validar funcionalidades, identificar problemas y recopilar feedback de los usuarios clave. El objetivo es minimizar riesgos, documentar incidencias y realizar los ajustes necesarios antes de la expansión total del sistema en la organización.

---

## Componentes Incluidos en el Piloto

- **Backend:**
- Despliegue de los servicios principales:
  - Autenticación y autorización
  - Gestión de usuarios
  - Gestión de roles y permisos
  - Ofertas
  - Propiedades
  - Mensajería y chat
  - Conversaciones
  - Notificaciones
  - Archivos y almacenamiento
  - Envío de correos electrónicos
  - Price History (historial de precios)
  - Verificaciones
  - Push notifications
  - Integración y pruebas de endpoints
 - Pruebas de endpoints básicos y seguridad.
   
   **Pruebas de endpoints básicos:**
   - Verificar que cada endpoint principal responde correctamente (GET, POST, PUT, DELETE).
   - Comprobar que los datos retornados tienen el formato esperado.
   - Validar que los endpoints gestionan correctamente los errores (404, 400, 500, etc.).
   - Testear la integración entre endpoints relacionados (por ejemplo, crear y luego consultar un recurso).
     

   **Pruebas de seguridad:**
   - Validar autenticación: solo usuarios autorizados pueden acceder a endpoints protegidos.
   - Verificar autorización: roles y permisos funcionan correctamente (por ejemplo, solo admin puede borrar usuarios).
   - Probar protección contra ataques comunes (SQL Injection, XSS, CSRF).
   - Comprobar que los datos sensibles no se exponen en las respuestas.
   - Testear rate limiting y bloqueo de IPs sospechosas si está implementado.

   **Herramientas y automatización:**
   - Uso de herramientas como Postman, Insomnia, o scripts automatizados con Jest/Supertest.
   - Documentar los casos de prueba y resultados.
 - Uso de scripts para roles y permisos.
   - Ejecución de scripts automatizados para crear roles y permisos en la base de datos.
   - Validación de que los roles y permisos se asignan correctamente a los usuarios.
   - Uso de los scripts `createAdminRole.js`, `createPermissions.js`, `createPermissionsByRole.js` para inicializar y verificar la configuración.
   - Documentar el proceso de ejecución de los scripts y los resultados obtenidos.
   - Comprobación de que los endpoints protegidos responden según el rol/permisos asignados.
   - Registro de incidencias o errores detectados durante la ejecución de los scripts.
 - Configuración de base de datos de pruebas.
   - Creación de una base de datos específica para pruebas (separada de la producción).
   - Configuración del archivo de conexión (`src/config/database.js` o `.env`) con credenciales y parámetros de entorno de pruebas.
   - Carga de datos de ejemplo o semilla para simular escenarios reales.
   - Validación de integridad y consistencia de los datos de prueba.
   - Automatización de la restauración o limpieza de la base de datos antes de cada ciclo de pruebas.
   - Documentar el proceso de configuración y restauración.
   - Registro de incidencias o errores detectados en la base de datos durante el piloto.
 - Monitoreo de logs y errores durante el piloto.
   - Configuración de sistemas de logging para todos los servicios y componentes del backend (autenticación, usuarios, roles, ofertas, propiedades, mensajería, notificaciones, archivos, correos, historial de precios, verificaciones, push notifications, etc.).
   - Revisión periódica de los logs generados por la aplicación, el servidor y los microservicios si aplica.
   - Identificación y registro de errores, advertencias y eventos relevantes en todos los módulos y controladores.
   - Uso de herramientas para visualizar y analizar logs (por ejemplo, Loggly, Papertrail, Kibana, o archivos locales).
   - Documentación de incidencias detectadas y acciones tomadas para su resolución en cada servicio.
   - Establecimiento de alertas para errores críticos o caídas del sistema en cualquier componente.
   - Validación de que los logs no exponen información sensible de usuarios, credenciales o datos privados.
   - Generación de reportes periódicos de errores y logs para revisión del equipo técnico.

 - **Frontend:**
   - Habilitación y despliegue de módulos esenciales:
     - Autenticación y registro de usuarios (LoginPage.js, RegisterPage.js, ProtectedRoute.js, RoleBasedRoute.js).
     - Dashboard principal y específicos (DashboardPage.js, AgentDashboard.js, BuyerDashboard.js, SellerDashboard.js).
     - Gestión de usuarios y agentes (AgentsPage.js, UserPermissionsInfo.js, agentsSlice.js).
     - Gestión de ofertas (OffersPage.js, MakeOfferModal.js, offersSlice.js).
     - Gestión de propiedades (PropertiesPage.js, CreatePropertyPage.js, AdvancedSearchBar.js, propertiesSlice.js).
     - Módulo de chat y mensajería (ChatPage.js, ChatWindow.js, ConversationsList.js, chatSlice.js).
     - Notificaciones (NotificationCenter.js, NotificationToast.js, notificationsSlice.js).
     - Verificaciones y badges (UserVerificationPage.js, VerificationBadges.js, verificationSlice.js).
     - Archivos y subida de documentos (FileUploadModal.js, fileService.js).
     - Configuración y privacidad (PrivacySettingsPage.js).
     - Navegación y layout (Navbar.js, Sidebar.js).
   - Pruebas de usabilidad y acceso para usuarios clave:
     - Validación de flujos de usuario en los módulos principales.
     - Pruebas de navegación, carga y visualización de datos.
     - Testeo de permisos y roles en la interfaz.
     - Pruebas de integración con el backend (apiClient.js y servicios).
     - Uso de herramientas como Cypress, Selenium, Testing Library o Jest para automatizar pruebas de UI y funcionalidad.
     - Documentación de casos de prueba y resultados.
   - Monitoreo de errores y logs en el frontend:
     - Configuración de captura de errores en la consola y reportes automáticos.
     - Uso de herramientas como Sentry, LogRocket o el propio navegador para registrar errores y advertencias.
     - Registro y documentación de incidencias detectadas por los usuarios piloto.
     - Validación de que los logs y reportes no exponen información sensible.

 - **Documentación:**
   - Manuales rápidos para usuarios piloto:
     - Elaboración de guías breves y visuales para las tareas principales (acceso, navegación, uso de módulos clave).
     - Instrucciones paso a paso para login, gestión de usuarios, ofertas, propiedades, chat y notificaciones.
     - Inclusión de capturas de pantalla y ejemplos prácticos.
     - Ubicación: `componentes/documentacion/`.
   - Guía de instalación y uso básico:
     - Documentación del proceso de instalación del sistema (backend y frontend) en el entorno piloto.
     - Requisitos previos, configuración de variables de entorno y dependencias.
     - Pasos para iniciar los servicios y acceder a la aplicación.
     - Solución de problemas comunes y FAQ.
     - Ubicación: `componentes/documentacion/README.md` y archivos relacionados.
   - Registro de incidencias y feedback:
     - Creación de un formato para registrar incidencias detectadas por los usuarios piloto.
     - Documentación de errores, sugerencias y mejoras propuestas durante el piloto.
     - Seguimiento y resolución de incidencias por el equipo técnico.
     - Ubicación: `componentes/documentacion/Haciendo/` o archivo específico de registro.

---

## Actividades Clave

1. Selección de usuarios clave para el piloto (ejemplo realista: comprador, vendedor e intermediador que usan la plataforma).
  - Criterios definidos: usuarios que representan los principales perfiles de uso de la plataforma (comprador, vendedor, intermediador), alta frecuencia de interacción y relevancia en los procesos de negocio.
  - Perfiles evaluados: comprador, vendedor e intermediador. Se seleccionaron estos tres por ser quienes utilizan directamente las funcionalidades principales del sistema.
  - Reunión realizada con representantes de cada perfil para explicar el objetivo del piloto, resolver dudas y obtener su compromiso.
  - Los perfiles de comprador, vendedor e intermediador fueron seleccionados como usuarios clave del piloto por su impacto y disposición a participar activamente.
  - La decisión se documentó y se comunicó oficialmente a los usuarios involucrados y al equipo técnico.
  - Responsables: Comprador (Ana Torres), Vendedor (Luis Gómez), Intermediador (Carla Ruiz).
2. Instalación y configuración de backend y frontend en entorno de pruebas.
   - Preparar el entorno de pruebas (servidor local, máquina virtual o ambiente cloud).
   - Clonar los repositorios de backend y frontend desde el repositorio oficial del proyecto.
   - Instalar dependencias necesarias:
     - Backend: ejecutar `npm install` en la carpeta `componentes/backend`.
     - Frontend: ejecutar `npm install` en la carpeta `componentes/frontend`.
   - Configurar archivos de entorno (`.env`) con las variables necesarias para pruebas (base de datos, credenciales, endpoints, etc.).
   - Inicializar la base de datos de pruebas y cargar datos de ejemplo si aplica.
   - Ejecutar scripts de inicialización para roles, permisos y verificaciones en el backend.
   - Levantar el servidor backend (`npm start` o comando correspondiente).
   - Levantar la aplicación frontend (`npm start` o comando correspondiente).
   - Verificar la conexión entre frontend y backend, y el acceso de los usuarios clave (comprador, vendedor, intermediador).
   - Documentar cualquier incidencia o ajuste realizado durante la instalación.
3. Capacitación breve a usuarios piloto.
  - Organizar una sesión de capacitación presencial o virtual para los usuarios clave (comprador, vendedor, intermediador).
  - Presentar los objetivos del piloto y el alcance de la plataforma Inmotech.
  - Explicar el funcionamiento de todos los módulos principales:
    - Autenticación y registro de usuarios (login, register, ProtectedRoute, RoleBasedRoute).
    - Dashboard principal y específicos (dashboard general, de agentes, compradores y vendedores).
    - Gestión de usuarios y agentes.
    - Gestión de ofertas.
    - Gestión de propiedades.
    - Chat y mensajería.
    - Notificaciones.
    - Verificaciones y badges.
    - Archivos y subida de documentos.
    - Configuración y privacidad.
    - Navegación y layout (Navbar, Sidebar).
  - Realizar demostraciones prácticas de las tareas más frecuentes para cada perfil.
  - Entregar manuales rápidos y guías visuales (ubicados en `componentes/documentacion/`).
  - Resolver dudas y recopilar sugerencias de los usuarios durante la capacitación.
  - Registrar la asistencia y el feedback recibido para ajustar el soporte y la documentación.
4. Ejecución de pruebas funcionales y técnicas.
  - Objetivo: Validar el funcionamiento real de todos los módulos del sistema (backend y frontend) con los usuarios piloto (comprador, vendedor, intermediador), identificar errores, incidencias y oportunidades de mejora.
  - Alcance: Pruebas sobre los módulos principales (autenticación, dashboard, gestión de usuarios/agentes, ofertas, propiedades, chat, notificaciones, verificaciones, archivos, configuración, privacidad, navegación/layout).
  - Pasos detallados:
    1. **Preparación de casos de prueba:**
      - Definir escenarios de uso realistas para cada perfil (comprador, vendedor, intermediador).
      - Elaborar checklist de funcionalidades a validar por módulo (login, registro, creación/edición de ofertas, consulta de propiedades, envío de mensajes, recepción de notificaciones, subida de archivos, etc.).
      - Documentar los casos de prueba en formato accesible para el equipo y los usuarios piloto.
    2. **Ejecución de pruebas funcionales:**
      - Los usuarios piloto realizan las tareas habituales en la plataforma, siguiendo los casos de prueba definidos.
      - Validar que cada módulo responde correctamente y permite completar los flujos principales sin errores.
      - Registrar cualquier incidencia, error, comportamiento inesperado o dificultad de uso.
      - Documentar los resultados de cada caso de prueba (éxito, fallo, observaciones).
    3. **Ejecución de pruebas técnicas:**
      - El equipo técnico realiza pruebas de integración entre frontend y backend (API, servicios, base de datos).
      - Validar tiempos de respuesta, carga de datos, manejo de errores y seguridad (autenticación, autorización, protección de datos sensibles).
      - Ejecutar pruebas automatizadas (Jest/Supertest en backend, Cypress/Testing Library en frontend) y registrar resultados.
      - Monitorear logs y reportes de errores en ambos entornos.
    4. **Pruebas de roles y permisos:**
      - Verificar que los permisos y restricciones funcionan según el perfil (por ejemplo, solo el intermediador puede aprobar ofertas, el comprador solo puede ver propiedades, etc.).
      - Probar acceso a endpoints protegidos y funcionalidades restringidas.
    5. **Pruebas de usabilidad y experiencia de usuario:**
      - Recopilar feedback de los usuarios piloto sobre facilidad de uso, navegación, claridad de la interfaz y velocidad de la plataforma.
      - Registrar sugerencias de mejora y dificultades encontradas.
    6. **Registro y documentación de incidencias:**
      - Utilizar el formato de registro de incidencias en `componentes/documentacion/Haciendo/` o archivo específico.
      - Documentar cada incidencia con detalle: módulo afectado, descripción, pasos para reproducir, capturas de pantalla si aplica.
      - Priorizar incidencias según impacto y frecuencia.
    7. **Generación de reportes de pruebas:**
      - Elaborar un reporte consolidado con los resultados de las pruebas funcionales y técnicas.
      - Incluir checklist de funcionalidades validadas, incidencias detectadas, sugerencias de los usuarios y acciones recomendadas.
      - Compartir el reporte con el equipo técnico y los usuarios piloto.
  - Herramientas recomendadas:
    - Backend: Postman, Insomnia, Jest, Supertest, revisión de logs.
    - Frontend: Cypress, Testing Library, Sentry, LogRocket, consola del navegador.
    - Documentación: Markdown, capturas de pantalla, checklist en Excel/Google Sheets.
  - Responsables:
    - Ejecución funcional: Usuarios piloto (Ana Torres, Luis Gómez, Carla Ruiz).
    - Ejecución técnica: Equipo de desarrollo y QA.
    - Registro y documentación: Responsable de pruebas y soporte.
  - Criterios de éxito:
    - Todos los módulos principales funcionan correctamente para los perfiles piloto.
    - Las incidencias críticas son identificadas y documentadas.
    - Se recopila feedback útil para ajustes y mejoras.
5. Recopilación de incidencias, sugerencias y resultados.
   - Objetivo: Centralizar y documentar todas las incidencias, sugerencias y resultados obtenidos durante el piloto para facilitar la toma de decisiones y la mejora continua.
   - Pasos detallados:
     1. **Registro de incidencias:**
        - Utilizar el formato de registro ubicado en `componentes/documentacion/Haciendo/` (por ejemplo, CONTROLADORES_MANEJO_ERRORES.md, ENDPOINTS_EXISTENCIA.md, o crear un archivo específico como `registro-incidencias-piloto.md`).
        - Documentar cada incidencia con los siguientes datos mínimos:
          - Módulo afectado (backend, frontend, documentación)
          - Descripción clara del problema
          - Pasos para reproducir
          - Usuario/perfil que la detectó
          - Fecha y hora
          - Captura de pantalla o evidencia (si aplica)
          - Estado (pendiente, en análisis, resuelta)
        - Priorizar incidencias según impacto y frecuencia.
     2. **Recopilación de sugerencias y feedback:**
        - Registrar todas las sugerencias y comentarios de los usuarios piloto (comprador, vendedor, intermediador) durante las pruebas y capacitación.
        - Utilizar un formato accesible (Markdown, Excel, Google Sheets) y ubicarlo en `componentes/documentacion/Haciendo/` o archivo específico.
        - Clasificar las sugerencias por módulo y tipo (mejora funcional, usabilidad, documentación, etc.).
        - Documentar el responsable de cada sugerencia y el seguimiento realizado.
     3. **Consolidación de resultados:**
        - Elaborar un reporte resumen con:
          - Incidencias detectadas y su estado
          - Sugerencias recibidas y acciones propuestas
          - Resultados de las pruebas funcionales y técnicas (checklist de funcionalidades validadas, módulos con problemas, observaciones generales)
        - Ubicar el reporte en `componentes/documentacion/Haciendo/` o en la carpeta principal de documentación.
        - Compartir el reporte con el equipo técnico y los usuarios piloto.
     4. **Seguimiento y cierre de incidencias:**
        - Asignar responsables para la resolución de cada incidencia.
        - Actualizar el estado de cada incidencia en el registro conforme se resuelvan.
        - Documentar las soluciones aplicadas y validar con los usuarios piloto.
     5. **Reunión de cierre del piloto:**
        - Organizar una reunión con todos los involucrados para presentar los resultados, discutir las incidencias y sugerencias, y definir los siguientes pasos.
        - Recopilar acuerdos y compromisos para la fase de ajustes y mejoras.
   - Herramientas recomendadas:
     - Markdown, Excel, Google Sheets para registros y reportes.
     - Capturas de pantalla y videos cortos para evidencias.
     - Repositorio de documentación centralizado (`componentes/documentacion/Haciendo/`).
   - Responsables:
     - Registro de incidencias: Responsable de pruebas y soporte.
     - Recopilación de sugerencias: Responsable de capacitación y usuarios piloto.
     - Consolidación de resultados: Líder de proyecto.
     - Seguimiento y cierre: Equipo técnico y QA.
   - Criterios de éxito:
     - Todas las incidencias y sugerencias están documentadas y priorizadas.
     - El reporte de resultados está disponible y compartido.
     - Los usuarios piloto participan activamente en el feedback y validación de soluciones.
6. Ajustes y mejoras antes de la siguiente fase.
  - Objetivo: Implementar las correcciones y mejoras identificadas durante el piloto, asegurando que el sistema esté listo para su expansión y uso generalizado.
  - Pasos detallados:
    1. **Análisis de incidencias y sugerencias:**
      - Revisar el reporte consolidado de incidencias y feedback generado en la etapa anterior.
      - Priorizar las incidencias críticas y las mejoras de alto impacto para su resolución inmediata.
    2. **Planificación de ajustes:**
      - Definir un plan de acción con responsables, plazos y recursos necesarios para cada ajuste o mejora.
      - Documentar el plan en un archivo accesible para el equipo (`componentes/documentacion/Haciendo/ajustes-mejoras-piloto.md`).
    3. **Implementación de correcciones:**
      - Realizar los cambios necesarios en el backend, frontend y documentación según el plan definido.
      - Validar que las correcciones resuelven las incidencias reportadas y no generan nuevos problemas.
      - Actualizar los manuales y guías si se modifican flujos o funcionalidades.
    4. **Pruebas de verificación:**
      - Ejecutar pruebas específicas sobre los módulos ajustados para confirmar la resolución de incidencias.
      - Involucrar a los usuarios piloto en la validación de las mejoras implementadas.
      - Documentar los resultados de las pruebas de verificación.
    5. **Actualización de documentación:**
      - Registrar todos los cambios realizados y las soluciones aplicadas en la documentación centralizada.
      - Actualizar el registro de incidencias con el estado final de cada caso.
    6. **Comunicación de resultados:**
      - Informar a todos los involucrados sobre los ajustes realizados y el estado del sistema.
      - Compartir el reporte final de ajustes y mejoras antes de iniciar la siguiente fase.
  - Herramientas recomendadas:
    - Markdown, Excel, Google Sheets para planes y reportes.
    - Repositorio de documentación centralizado (`componentes/documentacion/Haciendo/`).
  - Responsables:
    - Implementación de ajustes: Equipo técnico (backend, frontend, documentación).
    - Validación de mejoras: Usuarios piloto y QA.
    - Actualización de documentación: Responsable de documentación.
    - Comunicación de resultados: Líder de proyecto.
  - Criterios de éxito:
    - Todas las incidencias críticas están resueltas y validadas.
    - Las mejoras propuestas están implementadas y documentadas.
    - El sistema está listo para la expansión y uso generalizado.

---

## Criterios de Éxito

### Criterios de éxito detallados para el piloto
1. **Funcionamiento estable de los módulos principales:**
  - Todos los módulos (backend, frontend y documentación) operan sin errores críticos ni caídas.
  - Las funcionalidades clave (autenticación, dashboard, gestión de usuarios/agentes, ofertas, propiedades, chat, notificaciones, verificaciones, archivos, configuración, privacidad, navegación/layout) funcionan correctamente para los perfiles piloto.
  - Los logs y reportes no muestran incidencias graves ni datos sensibles expuestos.
2. **Feedback positivo de los usuarios piloto:**
  - Los usuarios piloto (comprador, vendedor, intermediador) reportan satisfacción con la experiencia de uso, facilidad de navegación y velocidad de la plataforma.
  - Las sugerencias y comentarios recibidos son mayormente de mejora y no de corrección de errores graves.
  - Se documenta el feedback en los formatos establecidos y se valida la participación activa de los usuarios.
3. **Identificación y resolución de problemas críticos:**
  - Todas las incidencias críticas detectadas durante el piloto son documentadas, priorizadas y resueltas antes de la siguiente fase.
  - Se valida la resolución de incidencias mediante pruebas de verificación y validación por parte de los usuarios piloto y el equipo técnico.
  - El registro de incidencias y soluciones está actualizado y disponible para consulta.
4. **Documentación completa y actualizada:**
  - Todos los manuales, guías y registros de incidencias están actualizados según los cambios realizados durante el piloto.
  - El equipo técnico y los usuarios piloto tienen acceso a la documentación relevante.
5. **Preparación para la expansión:**
  - El sistema está listo para ser implementado en otras áreas/departamentos, con los ajustes y mejoras validados.
  - Se cuenta con un reporte final de resultados, incidencias resueltas y mejoras implementadas.

---

## Hitos del Piloto
| Hito Clave                                 | Fecha Objetivo   | Estado       | Evidencia/Documento                      | Responsable           |
|--------------------------------------------|------------------|--------------|------------------------------------------|-----------------------|
| Selección de área piloto                   | 20/10/2025       | Completado    | Acta de reunión, lista de usuarios       | Líder de Proyecto     |
| Instalación y configuración de sistemas    | 21/10/2025       | Completado    | Registro de instalación, incidencias     | Equipo Técnico        |
| Capacitación a usuarios piloto             | 22/10/2025       | Completado    | Manuales entregados, lista de asistencia | Responsable de Cap.   |
| Pruebas funcionales y técnicas             | 23/10/2025       | Completado    | Reporte de pruebas, checklist            | Equipo de Pruebas     |
| Recopilación de incidencias y feedback     | 23/10/2025       | Completado    | Registro de incidencias y sugerencias    | Todos                 |
| Ajustes y mejoras                         | 24/10/2025       | En Curso      | Plan de ajustes, reporte de mejoras      | Equipo Técnico        |

---

## Cronograma de Actividades del Piloto
| ID | Actividad                                         | Responsable           | Inicio (Fecha) | Fin (Fecha)   |
|----|---------------------------------------------------|-----------------------|-----------------|--------------|
| 1  | Selección de área piloto                          | Líder de Proyecto     | 20/10/2025      | 20/10/2025   |
| 2  | Instalación y configuración de backend/frontend    | Equipo Técnico        | 21/10/2025      | 21/10/2025   |
| 3  | Capacitación a usuarios piloto                    | Responsable de Cap.   | 22/10/2025      | 22/10/2025   |
| 4  | Pruebas funcionales y técnicas                    | Equipo de Pruebas     | 23/10/2025      | 23/10/2025   |
| 5  | Recopilación de incidencias y feedback            | Todos                 | 23/10/2025      | 23/10/2025   |
| 6  | Ajustes y mejoras                                 | Equipo Técnico        | 24/10/2025      | 27/10/2025   |

---

## Riesgos y Mitigación en el Piloto
| Riesgo Identificado                      | Impacto | Responsable           | Plan de Contingencia/Mitigación                                  |
|------------------------------------------|---------|-----------------------|------------------------------------------------------------------|
| Resistencia de usuarios piloto           | Medio   | Responsable de Cap.   | Capacitación personalizada, comunicación clara, seguimiento de dudas y feedback. |
| Fallos en integración de módulos         | Alto    | Equipo Técnico        | Pruebas previas, checklist de integración, soporte técnico inmediato, revisión de logs. |
| Datos de prueba insuficientes            | Medio   | Equipo Técnico        | Preparar datos representativos antes de pruebas, cargar datos semilla, validar escenarios reales. |
| Incidencias no documentadas              | Bajo    | Responsable de Pruebas| Registro obligatorio de incidencias y soluciones, revisión periódica de registros. |
| Cambios no comunicados                   | Medio   | Líder de Proyecto     | Informes periódicos, reuniones de seguimiento, actualización de documentación y comunicación a usuarios. |
| Retrasos en ajustes y mejoras            | Medio   | Equipo Técnico        | Planificación detallada, asignación de responsables, seguimiento semanal de avances. |
| Problemas de seguridad                   | Alto    | Equipo Técnico        | Pruebas de seguridad, validación de roles/permisos, revisión de logs y reportes automáticos. |

---

## Documentos Relacionados
- Manual de usuario piloto (ubicado en `componentes/documentacion/`)
- Registro de incidencias y mejoras
- Checklist de pruebas y migración

---

**Checklist de implementación piloto:**
**Checklist de implementación piloto:**
- ✅ Seleccionar área/departamento piloto
- ✅ Instalar y configurar backend en entorno de pruebas
- ✅ Instalar y configurar frontend en entorno de pruebas
- ✅ Capacitar a usuarios piloto
- ✅ Ejecutar pruebas funcionales y técnicas
- ✅ Recopilar incidencias y feedback
- ⏳ Realizar ajustes y mejoras

> Este piloto permite validar el sistema Inmotech en condiciones reales, minimizando riesgos antes de la implantación total.
