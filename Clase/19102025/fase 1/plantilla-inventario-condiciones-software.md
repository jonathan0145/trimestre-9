# Plantilla 1: Inventario de Condiciones del Software

**Propósito:** Evaluar y documentar el estado actual del software, la infraestructura y los procesos asociados antes de iniciar un proyecto de implantación o migración de un nuevo sistema.

**Fecha de Elaboración:** 20/11/2025
**Elaborado por:** Jonathan Rendon, Nadia Masmela, Anderson Mora
**Sistema Evaluado:** INMOTECH (Backend Node.js, Frontend React, Base de datos MySQL/MariaDB)
**Versión Actual:** 1.0

## 1. Información General del Software Actual
Esta sección documenta los detalles fundamentales del sistema existente.

| Campo                                 | Descripción / Respuesta                                              |
|---------------------------------------|---------------------------------------------------------------------|
| Nombre del Proveedor/Desarrollador    | Desarrollo Interno (Equipo INMOTECH)                                |
| Tecnología Principal                  | Backend: Node.js (Express, Sequelize, Socket.io); Frontend: React; Base de datos: MySQL/MariaDB |
| Licenciamiento                        | Software libre, sin restricciones de usuarios.                      |
| Fecha de Última Actualización Mayor   | 20/11/2025                                                          |
| Manuales/Documentación Disponibles    | Sí. Ubicación: carpeta `documentacion/` y archivos `.md` en el repositorio. Estado: Completa y actualizada. |
| Nivel de Dependencia Operacional      | Alto. El sistema es crítico para la gestión inmobiliaria y la operación diaria. |

## 2. Infraestructura y Entorno Técnico
Detalles sobre el entorno donde se ejecuta actualmente el software.

| Componente                | Detalles Técnicos Actuales                                              | Observaciones / Restricciones                  |
|---------------------------|------------------------------------------------------------------------|-----------------------------------------------|
| Servidor (SO)             | Debian-web-server, versión estable.                                     | Hardware virtualizado, escalable según demanda.|
| Base de Datos (SGBD)      | MySQL/MariaDB 10.x, SQLite para pruebas.                               | Volumen de datos moderado, optimización periódica.|
| Integraciones/Interfaces  | Firebase (autenticación, notificaciones), API RESTful, sockets.        | Integración estable, monitoreo activo.         |
| Copia de Seguridad (Backup)| Diaria, total e incremental, almacenada en servidor y nube.            | Restauración rápida, copias redundantes.       |
| Hardware de Usuario Final | Mínimo: 4GB RAM, CPU dual-core, SO Windows/Linux/Mac, navegador Chrome/Firefox. | Sin restricciones graves, rendimiento óptimo en la mayoría de equipos. |

### 2.1 Inventario de Hardware
**Servidores:**
- CPU: 16 vCPU (virtualizados), RAM: 64GB, Almacenamiento: 2TB SSD
- Ubicación: Centro de datos virtualizado, escalable bajo demanda
**Estaciones de trabajo:**
- 20 equipos, CPU i5/i7, RAM 8GB, SSD 256GB, SO Windows 10/11 y Ubuntu 22.04
**Periféricos:**
- Impresoras multifunción (2 HP LaserJet), escáneres (1 Epson), conectados en red

### 2.2 Auditoría de Software
**Sistemas operativos:**
- Servidores: Debian 11 (estable), Estaciones: Windows 10/11, Ubuntu 22.04
**Aplicaciones existentes:**
- Navegadores Chrome/Firefox, Suite ofimática LibreOffice/Microsoft Office, herramientas de desarrollo (VS Code, Node.js, npm)
- No se detectan conflictos graves, integración estable con el sistema INMOTECH
**Bases de datos:**
- MySQL/MariaDB 10.x, SQLite para pruebas

### 2.3 Evaluación de Infraestructura de Red
**Ancho de banda:**
- 300 Mbps simétricos, latencia promedio <20ms, estabilidad alta
**Acceso remoto:**
- VPN corporativa configurada para acceso seguro de usuarios y administradores

### 2.4 Análisis de Seguridad
**Firewalls:**
- Firewall perimetral en el centro de datos, reglas restrictivas para puertos y servicios
**Antivirus:**
- ESET Endpoint Security en estaciones y servidores
**Permisos de usuario:**
- Gestión de roles y permisos en INMOTECH, acceso restringido por perfil
**Cumplimiento normativo:**
- LOPD (protección de datos personales), ISO 27001 (seguridad de la información), controles de acceso y auditoría

## 3. Funcionalidades Críticas y Personalizaciones
Identificación de las características esenciales y las modificaciones propias del sistema.

### A. Módulos y Funcionalidades Esenciales
Enumere los módulos y las funciones críticas que deben ser cubiertas por el nuevo sistema.

**Módulo 1:** Gestión de usuarios y agentes
  - Funcionalidades Críticas: Registro, autenticación, verificación, administración de roles y permisos.
**Módulo 2:** Gestión de propiedades y ofertas
  - Funcionalidades Críticas: Alta/baja/modificación de propiedades, publicación y gestión de ofertas, trazabilidad de transacciones.
**Módulo 3:** Comunicación y notificaciones
  - Funcionalidades Críticas: Chat en tiempo real, notificaciones push, historial de mensajes.
**Módulo 4:** Verificación y control de calidad
  - Funcionalidades Críticas: Checklist ISO 25010, análisis estático de código (SonarQube), reportes de calidad.

### B. Personalizaciones y Desarrollos a Medida
Describa las modificaciones específicas que se hicieron al sistema actual.

1. Personalización 1: Integración con Firebase para autenticación y notificaciones.
   - Motivo/Razón de Ser: Mejorar la seguridad y la experiencia de usuario.
   - Impacto de la No Migración: Alto. Sin esta integración, se pierde doble factor y notificaciones seguras.
2. Personalización 2: Implementación de checklist de calidad ISO 25010 y análisis SonarQube.
   - Motivo/Razón de Ser: Cumplimiento de estándares de calidad y trazabilidad.
   - Impacto de la No Migración: Medio. Se reduce la capacidad de auditar y mejorar la calidad del software.

## 4. Usuarios y Procesos de Negocio
Evaluación del uso, el conocimiento del sistema y la dependencia de los procesos.

| Aspecto                        | Detalles y Evaluación                                                  |
|--------------------------------|-----------------------------------------------------------------------|
| Número Total de Usuarios       | 50 usuarios registrados, 10-15 concurrentes promedio.                  |
| Nivel de Satisfacción del Usuario | Alto. Los usuarios valoran la automatización y la comunicación en tiempo real. |
| Conocimiento del Proceso (Usuarios) | Los usuarios siguen el proceso definido, con mínima tendencia a atajos. |
| Flujo de Trabajo Actual        | El sistema fuerza el flujo de trabajo, con validaciones y controles automáticos. |
| Resistencia al Cambio Esperada | Baja. La mayoría de los usuarios están involucrados en el desarrollo y adopción. |

## 5. Riesgos y Conclusiones Preliminares
Identificación de los puntos débiles y un resumen del estado general.

### A. Riesgos Identificados
Riesgo Técnico: Posible incompatibilidad futura con servicios externos o escalabilidad limitada si no se actualizan dependencias.
Riesgo Funcional: La trazabilidad de ofertas y propiedades depende de la correcta integración de módulos y validaciones.
Riesgo Humano: Dependencia del equipo actual para el conocimiento de integraciones y personalizaciones.

### B. Conclusiones y Recomendaciones
Resumen del estado del sistema y los pasos inmediatos sugeridos.

Estado General: El sistema es funcional, moderno y cumple con los requisitos actuales de gestión inmobiliaria. Mantiene buenas prácticas de seguridad y calidad.
Recomendación Clave: Documentar y mantener actualizadas las integraciones y personalizaciones críticas, así como los procedimientos de backup y recuperación.
Datos Críticos a Preservar/Migrar: Historial de transacciones, usuarios, propiedades, ofertas y registros de comunicación.
