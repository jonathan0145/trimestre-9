# PLANTILLA 4: CHECKLIST DE IMPLANTACIÓN

**Propósito:** Confirmar la realización y aprobación de todas las tareas críticas y entregables antes de avanzar a la siguiente fase del proyecto, especialmente la transición al entorno de producción (Go-Live).

**Proyecto:** [Nombre del Nuevo Sistema Implantado]
**Versión del Sistema:** [Número de Versión]
**Fecha de Revisión:** [Día/Mes/Año]
**Revisado por:** [Nombre del Gerente de Proyecto]

---

## I. Checklist de la FASE DE INFRAESTRUCTURA Y ENTORNO
Esta sección verifica que el entorno técnico esté listo y configurado correctamente para soportar el nuevo sistema.

| ID  | Tarea de Verificación                                                                 | Responsable   | Fecha Límite | Completado (Sí/No/NA) | Observaciones |
|-----|---------------------------------------------------------------------------------------|--------------|--------------|----------------------|--------------|
| I.1 | El servidor de producción ha sido instalado y configurado (SO y Hardware).            | IT           |              |                      |              |
| I.2 | La base de datos de producción ha sido instalada y optimizada (índices, seguridad).   | DBA          |              |                      |              |
| I.3 | El software del sistema ha sido instalado y licenciado en el entorno de producción.    | Proveedor/IT |              |                      |              |
| I.4 | Se ha implementado y probado la estrategia de respaldo (backup) y recuperación (restore). | IT       |              |                      |              |
| I.5 | Se han configurado y probado los accesos de red y firewall para usuarios finales.      | IT           |              |                      |              |
| I.6 | El ambiente de prueba (QA) está actualizado y es un espejo fiel del ambiente de producción. | IT       |              |                      |              |

---

## II. Checklist de la FASE DE CONFIGURACIÓN Y FUNCIONALIDAD
Verificación de que el sistema cumple con las especificaciones de negocio y que todas las funcionalidades críticas están activas.

| ID   | Tarea de Verificación                                                                 | Responsable         | Fecha Límite | Completado (Sí/No/NA) | Observaciones |
|------|---------------------------------------------------------------------------------------|---------------------|--------------|----------------------|--------------|
| II.1 | Se han configurado todos los parámetros y catálogos maestros del sistema.             | Consultor Funcional |              |                      |              |
| II.2 | Todos los perfiles de usuario y permisos de acceso han sido creados y asignados correctamente. | Consultor Funcional |         |                      |              |
| II.3 | Las integraciones con otros sistemas (ERP, CRM) han sido desarrolladas, probadas y están activas en producción. | Desarrollo | |                      |              |
| II.4 | Los reportes personalizados críticos han sido migrados y verificados.                  | Usuarios Clave      |              |                      |              |
| II.5 | El proceso de facturación/contabilidad ha sido verificado con transacciones de prueba. | Contabilidad        |              |                      |              |
| II.6 | Se ha completado la Prueba de Aceptación de Usuario (UAT) y ha sido firmada por los usuarios clave. | Gerente de Proyecto |      |                      |              |

---

## III. Checklist de la FASE DE MIGRACIÓN Y DATOS
Confirmación de que los datos antiguos se han trasladado de forma correcta y completa al nuevo sistema.

| ID    | Tarea de Verificación                                                                 | Responsable         | Fecha Límite | Completado (Sí/No/NA) | Observaciones |
|-------|---------------------------------------------------------------------------------------|---------------------|--------------|----------------------|--------------|
| III.1 | Los datos maestros (Clientes, Proveedores, Productos) han sido limpiados y aprobados para migración. | Usuarios Clave      |              |                      |              |
| III.2 | El plan de migración de datos históricos (si aplica) ha sido ejecutado exitosamente en el ambiente de prueba. | DBA |         |                      |              |
| III.3 | La migración final de datos maestros se ha ejecutado en el ambiente de producción.     | DBA                |              |                      |              |
| III.4 | Se ha verificado la integridad de los datos migrados (conciliación de saldos y registros clave). | Contabilidad/Finanzas |         |                      |              |
| III.5 | Se ha establecido la fecha de "corte" del sistema antiguo y la comunicación a todos los usuarios. | Gerente de Proyecto |         |                      |              |

---

## IV. Checklist de la FASE DE CAPACITACIÓN Y SOPORTE (PRE-GO-LIVE)
Verificación del conocimiento de los usuarios y la preparación del equipo de soporte para la transición.

| ID   | Tarea de Verificación                                                                 | Responsable         | Fecha Límite | Completado (Sí/No/NA) | Observaciones |
|------|---------------------------------------------------------------------------------------|---------------------|--------------|----------------------|--------------|
| IV.1 | La capacitación a todos los usuarios finales ha sido completada (asistencia > 90%).   | Capacitación        |              |                      |              |
| IV.2 | Los manuales de usuario y documentación de procesos están disponibles y distribuidos.  | Capacitación        |              |                      |              |
| IV.3 | El equipo de soporte post-implementación ha sido entrenado en el nuevo sistema y en el manejo de incidencias. | IT/Proveedor | |                      |              |
| IV.4 | Se ha comunicado el procedimiento de "Reporte de Errores" y el canal de soporte para el Go-Live. | Gerente de Proyecto |      |                      |              |
| IV.5 | Se ha firmado la aprobación final de la CHECKLIST DE IMPLANTACIÓN por parte de la Gerencia. | Comité Directivo |         |                      |              |

---

## V. Cierre del Go-Live y Monitoreo Inicial
Tareas a realizar inmediatamente después de la puesta en marcha.

| ID   | Tarea de Monitoreo                                                                 | Responsable         | Día 1 | Semana 1 | Semana 4 |
|------|------------------------------------------------------------------------------------|---------------------|-------|----------|----------|
| V.1  | Monitoreo del rendimiento de la base de datos y los servidores.                    | IT                  |       |          |          |
| V.2  | Verificación de que las transacciones clave se están registrando correctamente (ventas, compras, pagos). | Usuarios Clave | |          |          |
| V.3  | Generación y conciliación de los primeros reportes críticos (diarios y semanales). | Contabilidad        |       |          |          |
| V.4  | Recolección y resolución de las incidencias críticas (Nivel Alto) en menos de 24 horas. | Soporte         |       |          |          |
| V.5  | Sesión de lecciones aprendidas y cierre oficial del proyecto.                      | Gerente de Proyecto |       |          |          |
