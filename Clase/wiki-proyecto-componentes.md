# Wiki del Proyecto 'inmotech'

## Índice
- [Introducción](#introducción)
- [Estructura del Proyecto](#estructura-del-proyecto)
- [Backend](#backend)
- [Frontend](#frontend)
- [Modelos y Normas de Calidad](#modelos-y-normas-de-calidad)
- [Herramientas Utilizadas](#herramientas-utilizadas)
- [Checklist de Calidad](#checklist-de-calidad)
- [Reportes y Análisis](#reportes-y-análisis)
- [Recursos y Documentación](#recursos-y-documentación)
- [Plantilla Integral de Implantación](#plantilla-integral-de-implantación)
  - [1. Introducción](#1-introducción)
  - [2. Análisis de Condiciones del Sistema](#2-análisis-de-condiciones-del-sistema-fase-1--módulo-2)
  - [3. Estrategia de Implantación](#3-estrategia-de-implantación-fase-2--módulo-3)
  - [4. Cronograma y Hitos](#4-cronograma-y-hitos-fase-2--módulo-3)
  - [5. Asignación de Recursos](#5-asignación-de-recursos-fase-2)
  - [6. Planificación Técnica](#6-planificación-técnica-fase-2--módulo-4)
    - [6.1 Plan de Migración de Datos](#61-plan-de-migración-de-datos-ver-documento)
    - [6.2 Plan de Pruebas y Rollback](#62-plan-de-pruebas-y-rollback-ver-documento)
    - [6.3 Matriz de Riesgos y Planes de Contingencia](#63-matriz-de-riesgos-y-planes-de-contingencia)
  - [7. Ejecución y Monitoreo](#7-ejecución-y-monitoreo-fase-3--módulo-5)
    - [Checklist de Implantación y Validación Final](#checklist-de-implantación-y-validación-final)
  - [8. Soporte Post-Implantación](#8-soporte-post-implantación-fase-4--módulo-6)
  - [9. Integración y Presentación](#9-integración-y-presentación-módulo-7)
  - [Inmotech Móvil](#inmotech-móvil)

---

## Introducción
Este proyecto integra un sistema de gestión de propiedades con backend en Node.js y frontend en React, siguiendo buenas prácticas y modelos de calidad reconocidos.

## Estructura del Proyecto
- **backend/**: Servicios, controladores, modelos, rutas y utilidades.
- **frontend/**: Componentes, páginas, servicios y estilos.
- **documentacion/**: Requisitos, arquitectura, roles, casos de uso y API.
- **base-de-datos/**: Recursos para la gestión de datos.

## Backend
- Node.js y Express
- Controladores para entidades principales (usuarios, propiedades, ofertas, etc.)
- Autenticación y autorización
- Pruebas automatizadas (Jest)
- Documentación de API (Swagger)

## Frontend
- React
- Componentes reutilizables
- Páginas para gestión de usuarios, propiedades y ofertas
- Consumo de API REST
- Estilos y diseño responsivo

## Modelos y Normas de Calidad
- **ISO/IEC 25010**: Evaluación de calidad del software
- **Buenas prácticas de desarrollo**: Modularidad, documentación, pruebas

## Herramientas Utilizadas

Durante el proceso de validación de la app Inmotech, se han utilizado diversas herramientas que cumplen funciones clave dentro del ciclo de aseguramiento de calidad. Estas herramientas permiten gestionar pruebas, evaluar rendimiento, validar integración y documentar hallazgos. A continuación se describen las principales:

1. **Postman** – Pruebas de Integración y Validación de API  
   - Contribución a la calidad: Permite verificar que los servicios estén activos, respondan correctamente y cumplan con los contratos definidos. Es esencial para validar funcionalidad técnica antes de pruebas funcionales.

2. **Apache JMeter** – Pruebas de Rendimiento  
   - Contribución a la calidad: Evalúa la fiabilidad y escalabilidad del sistema bajo carga, detectando cuellos de botella y puntos críticos de latencia.

3. **Jest + Supertest** – Pruebas Automatizadas de API  
   - Función: Validar respuestas HTTP, estructuras de datos y comportamiento esperado de los endpoints.  
   - Aplicación: Se diseñaron y ejecutaron pruebas sobre módulos como Roles, Archivos, Mensajes, Citas e Historial de Precios. Todas fallaron por errores 403, 404 y 500.  
   - Contribución a la calidad: Automatiza la validación técnica, detecta errores en la lógica del backend y permite integración continua.

4. **Casos de Prueba Documentados (Excel / Artefactos)**  
   - Función: Planificar y estructurar pruebas funcionales manuales por módulo.  
   - Aplicación: Se diseñaron casos para Login, Registro, Gestión de Usuarios, Propiedades, Ventas/Publicación, Landing Page y módulos móviles (Favoritos, Chat).  
   - Contribución a la calidad: Asegura trazabilidad, cobertura funcional y permite registrar resultados, defectos y evidencias.

5. **Configuración de Peticiones HTTP (Manual / JMeter)**

Otras herramientas:
- **Google Forms**: Checklist de calidad
- **Google Sheets**: Reportes automáticos
- **SonarQube**: Análisis estático de código (opcional)
- **JIRA/TestRail**: Gestión de incidencias y pruebas (opcional)

## Checklist de Calidad
- Preguntas basadas en ISO 25010 para backend y frontend
- Evaluación de funcionalidad, fiabilidad, usabilidad, seguridad, mantenibilidad y portabilidad
- [Ver checklist](./12102025/actividad-3/checklist-calidad-ISO25010.md)

## Reportes y Análisis
- Resultados del checklist exportados y graficados
- Análisis de resultados y conclusiones
- [Ver análisis](./12102025/actividad-3/analisis-resultados-ISO25010.md)

## Recursos y Documentación
- [Requisitos del sistema](../documentacion/01-requisitos-del-sistema.md)
- [Arquitectura técnica](../documentacion/02-arquitectura-tecnica.md)
- [Roles y permisos](../documentacion/03-roles-y-permisos.md)
- [Casos de uso](../documentacion/04-casos-de-uso.md)
- [API documentación](../documentacion/05-api-documentacion.md)

---

## Plantilla Integral de Implantación

### 1. Introducción
- Objetivo del documento
- Proyecto
- Alcance del proyecto
- Equipo responsable

### 2. Análisis de Condiciones del Sistema (Fase 1 / Módulo 2)
- Inventario de hardware
- Auditoría de software
- Evaluación de infraestructura de red
- Análisis de seguridad

### 3. Estrategia de Implantación