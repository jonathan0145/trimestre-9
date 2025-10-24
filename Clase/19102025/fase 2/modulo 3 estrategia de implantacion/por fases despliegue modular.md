## Por fases: despliegue modular

Implementación progresiva del sistema Inmotech por módulos o áreas funcionales, permitiendo controlar riesgos y ajustar el proceso según los resultados de cada fase.

**Componentes incluidos en cada fase:**
Backend: servicios principales habilitados por módulo según la fase:
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

Frontend: páginas y componentes principales habilitados gradualmente:
	- Login y registro de usuarios
	- Dashboard principal y específicos (agentes, compradores, vendedores)
	- Gestión de usuarios y agentes
	- Gestión de ofertas
	- Gestión de propiedades
	- Chat y mensajería
	- Notificaciones
	- Verificaciones y badges
	- Archivos y subida de documentos
	- Configuración y privacidad
	- Navegación y layout (Navbar, Sidebar)

Documentación: manuales y guías específicas para cada módulo, checklist y registro de incidencias por fase.

**Actividades clave:**
---
1. Definir el orden y alcance de los módulos a implementar.

**Orden y alcance sugerido de los módulos/fases:**

1. Base de datos y migraciones
	- Estructura de tablas, relaciones, scripts de migración y restauración.
	- Documentación: modelo entidad-relación, instructivo de migración.
2. Autenticación y autorización
	- Backend: authController.js, middlewares de autenticación, endpoints de login/register.
	- Frontend: LoginPage.js, RegisterPage.js, rutas protegidas.
	- Documentación: guía de acceso y roles.
3. Gestión de usuarios y agentes
	- Backend: userController.js, agentController.js, modelos y rutas asociadas.
	- Frontend: páginas de gestión de usuarios y agentes, UserPermissionsInfo.js, agentsSlice.js.
	- Documentación: manual de administración de usuarios.
4. Gestión de roles y permisos
	- Backend: scripts de roles (createAdminRole.js, createPermissions.js, createPermissionsByRole.js), endpoints de permisos.
	- Frontend: interfaces para asignación de roles/permisos.
	- Documentación: tabla de roles y permisos.
5. Gestión de propiedades
	- Backend: propertyController.js, modelos y rutas de propiedades.
	- Frontend: PropertiesPage.js, CreatePropertyPage.js, AdvancedSearchBar.js.
	- Documentación: guía de alta y edición de propiedades.
6. Gestión de ofertas
	- Backend: offerController.js, endpoints de ofertas.
	- Frontend: OffersPage.js, MakeOfferModal.js.
	- Documentación: flujo de ofertas.
7. Mensajería y chat
	- Backend: chatController.js, conversationController.js, sockets.
	- Frontend: ChatPage.js, ChatWindow.js, ConversationsList.js.
	- Documentación: uso del chat.
8. Notificaciones
	- Backend: notificationController.js, servicios de notificaciones.
	- Frontend: NotificationCenter.js, NotificationToast.js.
	- Documentación: tipos y gestión de notificaciones.
9. Archivos y almacenamiento
	- Backend: fileController.js, endpoints de subida/descarga.
	- Frontend: FileUploadModal.js, fileService.js.
	- Documentación: formatos y límites de archivos.
10. Verificaciones y badges
	- Backend: verificationController.js, endpoints de verificación.
	- Frontend: UserVerificationPage.js, VerificationBadges.js.
	- Documentación: proceso de verificación.
11. Price History (historial de precios)
	- Backend: priceHistoryController.js, endpoints asociados.
	- Frontend: componentes de historial de precios.
	- Documentación: consulta y uso del historial.
12. Configuración y privacidad
	- Backend: endpoints de configuración.
	- Frontend: PrivacySettingsPage.js.
	- Documentación: opciones de privacidad.
13. Navegación y layout
	- Frontend: Navbar.js, Sidebar.js, estructura de navegación.
	- Documentación: mapa de navegación.
14. Push notifications y servicios adicionales
	- Backend: integración de push notifications.
	- Frontend: recepción y visualización.
	- Documentación: configuración de notificaciones push.
15. Integraciones externas
	- Backend/Frontend: APIs externas, servicios de terceros, pagos, etc.
	- Documentación: integración y pruebas de servicios externos.
16. Pruebas automatizadas y QA
	- Backend: tests en tests/ (Jest, Supertest, etc.).
	- Frontend: tests de componentes y flujos.
	- Documentación: reporte de cobertura y resultados.
17. Documentación técnica y manuales de usuario
	- Manuales, guías rápidas, instructivos de uso y administración.
18. Despliegue y monitoreo
	- Scripts de despliegue, logs, alertas, dashboards.
	- Documentación: instructivo de despliegue y monitoreo.

Cada fase incluye backend, frontend y documentación del módulo correspondiente, pruebas funcionales y técnicas, capacitación a usuarios y registro de incidencias.

---
2. Desplegar el primer módulo en ambiente de pruebas.

**Ejemplo: Despliegue del primer módulo - Autenticación y autorización**

1. Preparar el entorno de pruebas
	- Verificar base de datos de pruebas configurada (`.env`, `src/config/database.js`).
	- Asegurar variables de entorno necesarias para backend y frontend.

2. Backend
	- Ir a `componentes/backend/` y ejecutar `npm install`.
	- Verificar archivos `authController.js` y middlewares de autenticación en `src/controllers/` y `src/middlewares/`.
	- Iniciar backend con `npm start` o comando correspondiente.
	- Probar endpoints `/api/auth/login` y `/api/auth/register` con Postman o Insomnia.

3. Frontend
	- Ir a `componentes/frontend/` y ejecutar `npm install`.
	- Verificar componentes `LoginPage.js`, `RegisterPage.js` y rutas protegidas.
	- Iniciar frontend con `npm start`.
	- Acceder a login y registro desde el navegador y probar acceso.

4. Pruebas y validación
	- Realizar pruebas manuales de login y registro con diferentes usuarios.
	- Validar acceso a rutas protegidas solo tras autenticación.
	- Revisar logs de backend y frontend para detectar errores.

5. Documentación
	- Registrar incidencias en `componentes/documentacion/Haciendo/`.
	- Actualizar guía de acceso y roles si hubo cambios.

    en un despliegue modular ideal debes realizar este proceso detallado para cada uno de los 18 módulos/fases: preparar entorno, desplegar backend y frontend del módulo, probar, documentar incidencias y validar antes de pasar al siguiente.

---

3. Capacitar a los usuarios sobre el módulo correspondiente.

**Ejemplo: Capacitación de usuarios - Autenticación y autorización**

1. Preparar materiales de capacitación
	- Elaborar una guía rápida de acceso y roles (puede basarse en la documentación existente en `componentes/documentacion/`).
	- Incluir capturas de pantalla de `LoginPage.js` y `RegisterPage.js` mostrando el flujo de acceso y registro.
	- Explicar los tipos de usuarios y permisos básicos.

2. Realizar sesión de capacitación (presencial o virtual)
	- Presentar el objetivo del módulo: acceso seguro y controlado a la plataforma.
	- Demostrar en vivo el proceso de registro y login usando el frontend desplegado.
	- Mostrar cómo se restringe el acceso a rutas protegidas y qué ocurre si un usuario no autenticado intenta acceder.
	- Explicar el proceso de recuperación de contraseña si aplica.

3. Práctica guiada
	- Pedir a los usuarios que realicen el registro y login con sus propios datos de prueba.
	- Supervisar que puedan acceder correctamente y resolver dudas en el momento.

4. Recopilación de dudas y feedback
	- Registrar preguntas frecuentes y problemas encontrados durante la capacitación.
	- Anotar sugerencias de mejora para la interfaz o el flujo de autenticación.

5. Actualización de documentación
	- Incorporar las preguntas frecuentes y sugerencias en la guía de usuario.
	- Guardar la lista de asistencia y feedback en `componentes/documentacion/Haciendo/`.

---
4. Ejecutar pruebas funcionales y técnicas del módulo.

**Ejemplo: Pruebas funcionales y técnicas - Autenticación y autorización**

1. Pruebas funcionales (usuario final)
	- Probar el flujo de registro en `RegisterPage.js` con diferentes datos (usuario válido, usuario existente, campos vacíos).
	- Probar el flujo de login en `LoginPage.js` (usuario correcto, usuario incorrecto, contraseña errónea, usuario inactivo).
	- Validar que tras login exitoso se accede a las rutas protegidas y se muestra la información del usuario.
	- Intentar acceder a rutas protegidas sin autenticación y verificar que se redirige a login.
	- Probar el cierre de sesión y que se bloquea el acceso a rutas protegidas tras logout.

2. Pruebas técnicas (equipo de desarrollo/QA)
	- Usar Postman o Insomnia para enviar peticiones directas a los endpoints `/api/auth/login` y `/api/auth/register` con diferentes escenarios (válidos, inválidos, datos faltantes).
	- Revisar la respuesta de la API: códigos de estado, mensajes de error, estructura de los datos.
	- Ejecutar pruebas automatizadas si existen (por ejemplo, en `componentes/backend/tests/user.test.js` o `auth.test.js`).
	- Validar que los middlewares de autenticación funcionan correctamente (tokens, expiración, protección de rutas).
	- Revisar los logs del backend (`console`, archivos o herramientas externas) para detectar errores o advertencias.

3. Documentar resultados
	- Registrar los casos de prueba ejecutados y sus resultados en un checklist (puede ser en Markdown o Excel).
	- Documentar incidencias detectadas en `componentes/documentacion/Haciendo/`.
	- Anotar sugerencias de mejora o ajustes necesarios para el flujo de autenticación.

---
5. Recopilar incidencias y feedback de cada fase.

**Procedimiento detallado:**

1. Habilitar un canal de registro de incidencias y feedback
	- Utilizar el directorio `componentes/documentacion/Haciendo/` para centralizar los reportes.
	- Crear un archivo específico por módulo/fase (ejemplo: `incidencias-autenticacion.md`).
	- Alternativamente, habilitar un formulario digital (Google Forms, Microsoft Forms) para usuarios finales.

2. Registrar incidencias técnicas
	- El equipo de desarrollo documenta errores detectados durante pruebas y despliegue.
	- Incluir: descripción del problema, pasos para reproducirlo, capturas de pantalla o logs relevantes, responsable asignado y estado (abierto/en progreso/resuelto).
	- Ejemplo de formato:
	  - **Fecha:**
	  - **Módulo:**
	  - **Descripción:**
	  - **Pasos para reproducir:**
	  - **Responsable:**
	  - **Estado:**

3. Recopilar feedback de usuarios
	- Durante la capacitación y pruebas, solicitar a los usuarios que reporten dudas, dificultades o sugerencias.
	- Registrar comentarios positivos y negativos, así como propuestas de mejora.
	- Anotar la frecuencia de cada tipo de incidencia o sugerencia para priorizar ajustes.

4. Consolidar y analizar la información
	- Revisar periódicamente los archivos de incidencias y feedback.
	- Clasificar los reportes por tipo (crítico, menor, sugerencia) y por área (backend, frontend, documentación, capacitación).
	- Identificar patrones o problemas recurrentes.

5. Comunicar resultados y acciones
	- Compartir un resumen de incidencias y feedback con el equipo de desarrollo y responsables de cada módulo.
	- Definir responsables y plazos para la resolución de incidencias críticas antes de avanzar a la siguiente fase.
	- Documentar las acciones tomadas y actualizaciones realizadas en los archivos correspondientes.

6. Actualizar la documentación
	- Incorporar las lecciones aprendidas, preguntas frecuentes y soluciones en los manuales y guías de usuario.
	- Mantener un historial de incidencias resueltas para referencia futura.

---
6. Realizar ajustes antes de pasar al siguiente módulo.

**Procedimiento detallado:**

1. Analizar incidencias y feedback recopilados
	- Revisar el listado de incidencias técnicas y feedback de usuarios documentados en la fase anterior.
	- Priorizar los problemas críticos y las sugerencias recurrentes.

2. Definir acciones correctivas y de mejora
	- Asignar responsables para cada incidencia o mejora prioritaria.
	- Establecer plazos realistas para la resolución de cada punto antes de avanzar.
	- Documentar las acciones a realizar en el archivo de incidencias del módulo correspondiente.

3. Implementar los ajustes en backend, frontend y documentación
	- Corregir errores detectados en el código (controladores, endpoints, componentes, etc.).
	- Mejorar flujos de usuario, mensajes, validaciones o interfaz según el feedback recibido.
	- Actualizar manuales, guías y materiales de capacitación si hubo cambios relevantes.

4. Validar los ajustes realizados
	- Repetir las pruebas funcionales y técnicas sobre los puntos ajustados.
	- Confirmar con usuarios clave que las mejoras resuelven los problemas reportados.
	- Registrar los resultados de la validación en el archivo de incidencias.

5. Cerrar incidencias resueltas y actualizar el historial
	- Marcar como resueltas las incidencias solucionadas.
	- Mantener actualizado el historial de incidencias y mejoras implementadas para referencia futura.

6. Decidir avance a siguiente módulo
	- Solo avanzar si los problemas críticos han sido resueltos y los usuarios validan el funcionamiento del módulo.
	- Documentar la decisión de avance y comunicar al equipo.

---
7. Repetir el proceso para cada módulo hasta completar el sistema.

**Procedimiento detallado:**

1. Seleccionar el siguiente módulo según el orden definido
	- Consultar la lista de módulos/fases y verificar que el anterior fue validado y ajustado.

2. Preparar el entorno para el nuevo módulo
	- Configurar variables, dependencias y datos de prueba específicos si aplica.
	- Actualizar documentación técnica previa si es necesario.

3. Desplegar backend y frontend del módulo
	- Implementar y habilitar los servicios, endpoints y componentes correspondientes.
	- Verificar integración con módulos ya desplegados.

4. Capacitar a los usuarios sobre el nuevo módulo
	- Preparar materiales y realizar sesiones de capacitación específicas.
	- Recoger dudas y sugerencias durante la capacitación.

5. Ejecutar pruebas funcionales y técnicas
	- Realizar pruebas manuales y automatizadas sobre los nuevos flujos y funcionalidades.
	- Documentar resultados y detectar incidencias.

6. Recopilar incidencias y feedback
	- Registrar errores, dudas y sugerencias en el canal habilitado.
	- Analizar y priorizar los reportes recibidos.
	- Documentar la decisión y comunicar al equipo.
	---

	## Criterios de éxito globales del proyecto Inmotech

	1. Todos los módulos implementados cumplen con los requisitos funcionales y no funcionales definidos en la documentación.
	2. El sistema es estable, seguro y escalable, con integración correcta entre backend y frontend.
	3. Los usuarios finales pueden operar cada módulo según su perfil, con capacitación y materiales adecuados.
	4. Las incidencias críticas detectadas en cada fase han sido resueltas antes de avanzar.
	5. La documentación técnica y de usuario está completa, actualizada y accesible.
	6. El sistema ha superado pruebas funcionales, técnicas y de integración, con evidencia documentada.
	7. El monitoreo y los mecanismos de soporte están activos para la operación continua.
	8. El feedback de usuarios y stakeholders ha sido incorporado en mejoras clave antes del cierre del proyecto.
	9. El despliegue se realizó sin interrupciones graves en el servicio y con comunicación efectiva al equipo y usuarios.
	10. Se cuenta con un registro histórico de incidencias, mejoras y decisiones tomadas durante todo el proceso.

9. Repetir el ciclo hasta completar todos los módulos
	- Mantener actualizado el checklist de avance y la documentación de cada fase.

---

**Criterios de éxito globales del proyecto Inmotech:**
1. Todos los módulos implementados cumplen con los requisitos funcionales y no funcionales definidos en la documentación.
2. El sistema es estable, seguro y escalable, con integración correcta entre backend y frontend.
3. Los usuarios finales pueden operar cada módulo según su perfil, con capacitación y materiales adecuados.
4. Las incidencias críticas detectadas en cada fase han sido resueltas antes de avanzar.
5. La documentación técnica y de usuario está completa, actualizada y accesible.
6. El sistema ha superado pruebas funcionales, técnicas y de integración, con evidencia documentada.
7. El monitoreo y los mecanismos de soporte están activos para la operación continua.
8. El feedback de usuarios y stakeholders ha sido incorporado en mejoras clave antes del cierre del proyecto.
9. El despliegue se realizó sin interrupciones graves en el servicio y con comunicación efectiva al equipo y usuarios.
10. Se cuenta con un registro histórico de incidencias, mejoras y decisiones tomadas durante todo el proceso.

---

## Hitos Clave del Despliegue Modular
| Hito Clave                                 | Fase/Módulo                  | Fecha Objetivo   | Estado       | Evidencia/Documento                      | Responsable           |
|--------------------------------------------|------------------------------|------------------|--------------|------------------------------------------|-----------------------|
| Definición de orden y alcance              | General                      | 25/10/2025       | Pendiente    | Documento de alcance, lista de módulos   | Líder de Proyecto     |
| Instalación y configuración de entorno     | Base de datos y migraciones  | 26/10/2025       | Pendiente    | Registro de instalación, incidencias     | Equipo Técnico        |
| Despliegue de autenticación y autorización | Autenticación y autorización | 27/10/2025       | Pendiente    | Checklist de despliegue, logs            | Equipo Técnico        |
| Capacitación usuarios módulo autenticación | Autenticación y autorización | 28/10/2025       | Pendiente    | Manuales, lista de asistencia            | Responsable de Cap.   |
| Pruebas y validación de módulo             | Autenticación y autorización | 29/10/2025       | Pendiente    | Reporte de pruebas, checklist            | Equipo de Pruebas     |
| Recopilación de incidencias y feedback     | Autenticación y autorización | 29/10/2025       | Pendiente    | Registro de incidencias y sugerencias    | Todos                 |
| Ajustes y mejoras del módulo               | Autenticación y autorización | 30/10/2025       | Pendiente    | Plan de ajustes, reporte de mejoras      | Equipo Técnico        |
| ...                                        | ...                          | ...              | ...          | ...                                      | ...                   |

---

## Cronograma de Actividades Modular (Ejemplo por Fase)
| ID | Actividad                                         | Fase/Módulo                  | Responsable           | Inicio (Fecha) | Fin (Fecha)   |
|----|---------------------------------------------------|------------------------------|-----------------------|-----------------|--------------|
| 1  | Definir orden y alcance de módulos                | General                      | Líder de Proyecto     | 25/10/2025      | 25/10/2025   |
| 2  | Instalación y configuración de entorno            | Base de datos y migraciones  | Equipo Técnico        | 26/10/2025      | 26/10/2025   |
| 3  | Despliegue de autenticación y autorización        | Autenticación y autorización | Equipo Técnico        | 27/10/2025      | 27/10/2025   |
| 4  | Capacitación usuarios módulo autenticación        | Autenticación y autorización | Responsable de Cap.   | 28/10/2025      | 28/10/2025   |
| 5  | Pruebas y validación de módulo                    | Autenticación y autorización | Equipo de Pruebas     | 29/10/2025      | 29/10/2025   |
| 6  | Recopilación de incidencias y feedback            | Autenticación y autorización | Todos                 | 29/10/2025      | 29/10/2025   |
| 7  | Ajustes y mejoras del módulo                      | Autenticación y autorización | Equipo Técnico        | 30/10/2025      | 31/10/2025   |
| ...| ...                                               | ...                          | ...                   | ...             | ...          |

---

## Riesgos y Mitigación en el Despliegue Modular
| Riesgo Identificado                      | Fase/Módulo                  | Impacto | Responsable           | Plan de Contingencia/Mitigación                                  |
|------------------------------------------|------------------------------|---------|-----------------------|------------------------------------------------------------------|
| Resistencia de usuarios                  | Todas                        | Medio   | Responsable de Cap.   | Capacitación personalizada, comunicación clara, seguimiento de dudas y feedback. |
| Fallos en integración de módulos         | Todas                        | Alto    | Equipo Técnico        | Pruebas previas, checklist de integración, soporte técnico inmediato, revisión de logs. |
| Datos de prueba insuficientes            | Todas                        | Medio   | Equipo Técnico        | Preparar datos representativos antes de pruebas, cargar datos semilla, validar escenarios reales. |
| Incidencias no documentadas              | Todas                        | Bajo    | Responsable de Pruebas| Registro obligatorio de incidencias y soluciones, revisión periódica de registros. |
| Cambios no comunicados                   | Todas                        | Medio   | Líder de Proyecto     | Informes periódicos, reuniones de seguimiento, actualización de documentación y comunicación a usuarios. |
| Retrasos en ajustes y mejoras            | Todas                        | Medio   | Equipo Técnico        | Planificación detallada, asignación de responsables, seguimiento semanal de avances. |
| Problemas de seguridad                   | Todas                        | Alto    | Equipo Técnico        | Pruebas de seguridad, validación de roles/permisos, revisión de logs y reportes automáticos. |
| ...                                      | ...                          | ...     | ...                   | ...                                                              |

---

## Documentos Relacionados por Fase
- Manual de usuario de cada módulo (ubicado en `componentes/documentacion/`)
- Registro de incidencias y mejoras por módulo
- Checklist de pruebas y migración por fase
- Actas de reunión y comunicación de cambios
- Reportes de validación y capacitación

**Checklist de despliegue modular:**
- [ ] Definir orden y alcance de los módulos
- [ ] Definir responsables y cronograma de cada fase
- [ ] Preparar entorno de pruebas y producción
- [ ] Desplegar backend y frontend del módulo
- [ ] Validar integración con módulos previos
- [ ] Capacitar usuarios del módulo
- [ ] Ejecutar pruebas funcionales y técnicas
- [ ] Documentar resultados de pruebas y validaciones
- [ ] Recopilar incidencias y feedback
- [ ] Realizar ajustes y mejoras
- [ ] Actualizar documentación técnica y de usuario tras cada fase
- [ ] Comunicar avances y cambios al equipo y usuarios clave
- [ ] Registrar y cerrar incidencias en el historial
- [ ] Repetir proceso para siguiente módulo
