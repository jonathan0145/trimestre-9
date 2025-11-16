# Plantilla 1: Inventario de Condiciones del Software

**Propósito:** Evaluar y documentar el estado actual del software, la infraestructura y los procesos asociados antes de iniciar un proyecto de implantación o migración de un nuevo sistema.

**Fecha de Elaboración:** [Día/Mes/Año]
**Elaborado por:** [Nombre del Responsable/Equipo]
**Sistema Evaluado:** [Nombre del Sistema/Módulo Actual]
**Versión Actual:** [Número de Versión]

## 1. Información General del Software Actual
Esta sección documenta los detalles fundamentales del sistema existente.

| Campo                                 | Descripción / Respuesta                                              |
|---------------------------------------|---------------------------------------------------------------------|
| Nombre del Proveedor/Desarrollador    | [Ej: Microsoft, SAP, Desarrollo Interno, etc.]                      |
| Tecnología Principal                  | [Ej: Java, .NET, PHP, Legado (COBOL, FoxPro), etc.]                |
| Licenciamiento                        | [Tipo de licencia y cantidad de usuarios/puestos.]                  |
| Fecha de Última Actualización Mayor   | [Día/Mes/Año]                                                       |
| Manuales/Documentación Disponibles    | [Sí/No. ¿Ubicación? ¿Estado de la documentación (Completa, Obsoleta)?] |
| Nivel de Dependencia Operacional      | [Bajo, Medio, Alto. ¿Qué tan crítico es para el negocio?]           |

## 2. Infraestructura y Entorno Técnico
Detalles sobre el entorno donde se ejecuta actualmente el software.

| Componente                | Detalles Técnicos Actuales                                              | Observaciones / Restricciones                  |
|---------------------------|------------------------------------------------------------------------|-----------------------------------------------|
| Servidor (SO)             | [Ej: Windows Server 2019, RHEL 8, etc. – Versión y Edición]            | [Ej: No hay soporte extendido, hardware obsoleto.] |
| Base de Datos (SGBD)      | [Ej: SQL Server 2017, Oracle 19c, MySQL 5.7 - Versión y Edición]       | [Ej: Alto volumen de datos (TBs), requiere optimización.] |
| Integraciones/Interfaces  | [Sistemas con los que se comunica (Ej: ERP, CRM, Contabilidad).]       | [Ej: Utiliza una API REST/Archivo plano (CSV/XML), estado de la conexión.] |
| Copia de Seguridad (Backup)| [Frecuencia (Diaria/Semanal), Tipo (Total/Incremental), Ubicación.]   | [Ej: Restauración lenta, copias en sitio único.] |
| Hardware de Usuario Final | [Requisitos Mínimos (RAM, CPU, SO, Navegador).]                        | [Ej: Equipos con bajo rendimiento en ciertas áreas.] |

## 3. Funcionalidades Críticas y Personalizaciones
Identificación de las características esenciales y las modificaciones propias del sistema.

### A. Módulos y Funcionalidades Esenciales
Enumere los módulos y las funciones críticas que deben ser cubiertas por el nuevo sistema.

- **Módulo 1:** [Nombre del Módulo – Ej: Gestión de Inventario]
  - Funcionalidades Críticas: [Ej: Cálculo de costos promedio, Recuento cíclico.]
- **Módulo 2:** [Nombre del Módulo – Ej: Facturación]
  - Funcionalidades Críticas: [Ej: Emisión electrónica, Reportes fiscales.]
- **Módulo 3:** [Nombre del Módulo – Ej: Recursos Humanos]
  - Funcionalidades Críticas: [Ej: Cálculo de nómina con reglas especiales.]

### B. Personalizaciones y Desarrollos a Medida
Describa las modificaciones específicas que se hicieron al sistema actual.

1. Personalización 1: [Descripción – Ej: Reporte de ventas especial no estándar.]
   - Motivo/Razón de Ser: [¿Por qué se creó? Ej: Requerimiento legal local.]
   - Impacto de la No Migración: [Bajo, Medio, Alto – ¿Qué pasa si no existe en el nuevo sistema?]
2. Personalización 2: [Descripción – Ej: Campo extra en la ficha de cliente para “Clasificación de riesgo”]
   - Motivo/Razón de Ser: [¿Por qué se creó? Ej: Proceso interno de la empresa.]
   - Impacto de la No Migración: [Bajo, Medio, Alto.]

## 4. Usuarios y Procesos de Negocio
Evaluación del uso, el conocimiento del sistema y la dependencia de los procesos.

| Aspecto                        | Detalles y Evaluación                                                  |
|--------------------------------|-----------------------------------------------------------------------|
| Número Total de Usuarios       | [Cantidad de usuarios con acceso y usuarios concurrentes promedio.]    |
| Nivel de Satisfacción del Usuario | [Bajo, Medio, Alto. Razones principales de la insatisfacción (si aplica).] |
| Conocimiento del Proceso (Usuarios) | [¿Los usuarios siguen el proceso o usan “atajos” en el sistema?]     |
| Flujo de Trabajo Actual        | [¿El sistema actual fuerza el flujo de trabajo o es muy flexible/manual?] |
| Resistencia al Cambio Esperada | [Baja, Media, Alta. ¿Quiénes son los principales reticentes (Stakeholders)?] |

## 5. Riesgos y Conclusiones Preliminares
Identificación de los puntos débiles y un resumen del estado general.

### A. Riesgos Identificados
- Riesgo Técnico: [Ej: La base de datos actual no es compatible con el nuevo sistema y requiere una migración compleja de datos.]
- Riesgo Funcional: [Ej: La función crítica de cálculo de costos es única y el nuevo sistema no la contempla de serie.]
- Riesgo Humano: [Ej: El usuario clave que conoce las personalizaciones se jubila en 6 meses.]

### B. Conclusiones y Recomendaciones
Resumen del estado del sistema y los pasos inmediatos sugeridos.

- Estado General: [Ej: El sistema es funcional pero obsoleto. Requiere reemplazo urgente por riesgo de falla.]
- Recomendación Clave: [Ej: Dar prioridad a la documentación de las personalizaciones críticas antes de iniciar la fase de Discovery del nuevo software.]
- Datos Críticos a Preservar/Migrar: [Ej: Historial de ventas de los últimos 5 años, Maestros de clientes y proveedores.]
