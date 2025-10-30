# PLANTILLA 2: PLAN DE IMPLANTACIÓN - CRONOGRAMA

**Propósito:** Definir las fases, tareas, responsables y plazos necesarios para la exitosa puesta en marcha del nuevo sistema de software.

**Proyecto:** [Nombre del Nuevo Sistema a Implantar]
**Objetivo Principal:** [Ej: Migrar el sistema de contabilidad legacy a SAP Business One en 6 meses.]

**Fecha de Inicio Estimada:** [Día/Mes/Año]
**Fecha de Puesta en Vivo (Go-Live) Objetivo:** [Día/Mes/Año]

---

## 1. Resumen de Hitos del Proyecto

| Hito Clave                                      | Fecha Objetivo   | Estado                              |
|-------------------------------------------------|------------------|-------------------------------------|
| Fase 1: Aprobación de Alcance                   | [Día/Mes/Año]    | [Completado/En Curso/Pendiente]     |
| Fase 2: Instalación de Entorno de Prueba (QA)   | [Día/Mes/Año]    | [Completado/En Curso/Pendiente]     |
| Fase 3: Carga y Pruebas de Datos Maestros       | [Día/Mes/Año]    | [Completado/En Curso/Pendiente]     |
| Fase 4: Aprobación de Pruebas UAT (Usuarios)    | [Día/Mes/Año]    | [Completado/En Curso/Pendiente]     |
| PUESTA EN VIVO (GO-LIVE)                        | [Día/Mes/Año]    | [Completado/En Curso/Pendiente]     |

---

## 2. Cronograma Detallado por Fases

El cronograma se divide en fases que garantizan una progresión lógica y controlada del proyecto.

### FASE I: INICIO Y PLANIFICACIÓN (Semanas 1 - 4)

| ID Tarea | Tarea                                                                 | Responsable              | Inicio (Semana) | Fin (Semana) |
|----------|-----------------------------------------------------------------------|--------------------------|-----------------|--------------|
| 1.1      | Kick-Off oficial del proyecto y presentación del equipo.              | Gerente de Proyecto      | 1               | 1            |
| 1.2      | Definición y aprobación final del alcance (scope).                    | Comité Directivo         | 1               | 2            |
| 1.3      | Levantamiento detallado de requerimientos funcionales (Workshops).    | Consultor Funcional      | 2               | 4            |
| 1.4      | Revisión y documentación de las brechas (Gaps) entre sistema actual y nuevo. | Equipo Funcional/IT | 3               | 4            |
| 1.5      | Configuración de los permisos y accesos de los usuarios clave al entorno de prueba. | IT/Proveedor | 4               | 4            |

### FASE II: CONFIGURACIÓN Y DESARROLLO (Semanas 5 - 12)

| ID Tarea | Tarea                                                                 | Responsable              | Inicio (Semana) | Fin (Semana) |
|----------|-----------------------------------------------------------------------|--------------------------|-----------------|--------------|
| 2.1      | Instalación y configuración del entorno de pruebas (QA).              | IT                       | 5               | 5            |
| 2.2      | Configuración inicial de parámetros del sistema (catálogos, roles, permisos). | Consultor Funcional | 5               | 8            |
| 2.3      | Desarrollo o ajuste de integraciones con sistemas externos (si aplica). | Equipo de Desarrollo   | 6               | 10           |
| 2.4      | Desarrollo de reportes personalizados y formatos de impresión.         | Equipo de Desarrollo     | 9               | 12           |
| 2.5      | Preparación y limpieza de datos maestros para migración (clientes, productos, etc.). | Usuarios Clave | 10              | 12           |

### FASE III: PRUEBAS Y CAPACITACIÓN (Semanas 13 - 18)

| ID Tarea | Tarea                                                                 | Responsable              | Inicio (Semana) | Fin (Semana) |
|----------|-----------------------------------------------------------------------|--------------------------|-----------------|--------------|
| 3.1      | Migración de prueba de datos maestros al entorno QA.                  | IT                       | 13              | 13           |
| 3.2      | Pruebas Funcionales Integrales (Consultor y Usuarios Clave).          | Consultor Funcional      | 14              | 15           |
| 3.3      | Desarrollo del material de capacitación y manuales de usuario.        | Capacitación             | 15              | 16           |
| 3.4      | Capacitación a usuarios finales (sesiones por módulo).                | Capacitación             | 17              | 18           |
| 3.5      | Pruebas de Aceptación de Usuario (UAT) y firma de aprobación.         | Usuarios Finales         | 18              | 18           |

### FASE IV: PUESTA EN VIVO (GO-LIVE) Y CIERRE (Semanas 19 - 22)

| ID Tarea | Tarea                                                                 | Responsable              | Inicio (Semana) | Fin (Semana) |
|----------|-----------------------------------------------------------------------|--------------------------|-----------------|--------------|
| 4.1      | Instalación y configuración final del entorno de Producción.          | IT                       | 19              | 19           |
| 4.2      | Migración final de datos maestros e históricos (si aplica).           | IT                       | 19              | 20           |
| 4.3      | Inicio de Operación (Go-Live).                                        | Usuarios Finales         | 20              | 20           |
| 4.4      | Soporte post-implementación (Periodo de estabilización).              | Proveedor/IT             | 20              | 22           |
| 4.5      | Cierre oficial del proyecto y lecciones aprendidas.                   | Gerente de Proyecto      | 22              | 22           |

---

## 3. Plan de Mitigación de Riesgos

Lista de los principales riesgos identificados y la acción preventiva asociada.

| Riesgo Identificado                        | Impacto (Alto/Medio/Bajo) | Plan de Contingencia/Mitigación                                                                 |
|---------------------------------------------|---------------------------|-------------------------------------------------------------------------------------------------|
| Retraso en la Migración de Datos            | Alto                      | Asignar un segundo especialista en datos y validar la estructura de la BD antes de la Fase III. |
| Resistencia al Cambio de Usuarios Clave     | Medio                     | Realizar sesiones de coaching individual y nombrar “Super Usuarios” con incentivos.             |
| Brecha Funcional Crítica No Identificada    | Alto                      | Incluir una revisión de 4 ojos (Consultor y Usuario) en la aprobación de la Tarea 1.3.          |
| Fallos de Integración con el ERP/CRM        | Medio                     | Establecer un entorno de pruebas espejo para simular transacciones en vivo antes del Go-Live.    |
