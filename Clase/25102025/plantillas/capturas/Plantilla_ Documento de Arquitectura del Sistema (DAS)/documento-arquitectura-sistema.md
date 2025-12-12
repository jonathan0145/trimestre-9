# Plantilla: Documento de Arquitectura del Sistema (DAS)

**Título del Proyecto:** [Nombre del Nuevo Proyecto]
**Versión:** 1.0
**Fecha:** [Fecha de Creación]
**Elaborado por:** Nadia Masmela, Anderson Mora y Jonathan Ivan Rendon Bermeo

---

## 0. Historial de Versiones
| Versión | Fecha  | Autor(es)  | Descripción del Cambio         |
|---------|--------|-------------------------------|-------------------------------|
| 1.0     | [Fecha]| Nadia Masmela, Anderson Mora y Jonathan Ivan Rendon Bermeo | Creación del documento        |

---

## Tabla de Contenido
1. Introducción
2. Problema
3. Solución Propuesta
4. Objetivos
   4.1. Objetivo general
   4.2. Objetivos específicos
5. Propuesta Arquitectónica
   5.1. Definición y Estilo de la Arquitectura
   5.2. Perspectiva Lógica (Vista de Casos de Uso)
   5.3. Perspectiva del Desarrollo (Despliegue e Implementación)
   5.4. Requisitos de Calidad
6. Definiciones Técnicas
   6.1. Lenguajes de Programación y Frameworks
   6.2. Lenguaje de Programación de Base de Datos
   6.3. Extensiones y Librerías Requeridas
7. Requisitos de Hardware y Despliegue
8. Consideraciones

---

## 1. Introducción
[Describir el contexto general del proyecto, la empresa o área para la que se desarrollará el sistema y la justificación de la necesidad de esta nueva solución.]

## 2. Problema
[Describir el problema o la necesidad actual que motiva el desarrollo de este nuevo sistema. El documento de base lo enfoca en cómo se maneja la información actualmente (ej. de manera manual, ofimática) y las inconsistencias o problemas de seguridad que esto genera.]

## 3. Solución Propuesta
[Presentar una visión de alto nivel de la solución tecnológica. Indicar el tipo de solución (ej. aplicación web, móvil, de escritorio, microservicios) y los beneficios principales que aportará, como la dinamización de procesos y la gestión sistematizada de la información.]

## 4. Objetivos
### 4.1. Objetivo general
[Definir la meta principal y el alcance del sistema en una frase clara y medible.]

### 4.2. Objetivos específicos
[Enumerar las metas detalladas y concretas que se deben lograr para cumplir con el objetivo general. Estos deben ser medibles (ej. "Permitir la gestión de reportes", "Diseñar un esquema arquitectónico").]

## 5. Propuesta Arquitectónica
El objeto de esta sección es sustentar la solución propuesta desde la arquitectura técnica, sirviendo como referencia para el diseño y análisis de la implementación.

### 5.1. Definición y Estilo de la Arquitectura
- **Estilo Principal:** (Ej. Arquitectura Orientada a Servicios (SOA), Microservicios, Monolítica, Capas).
- **Patrones de Diseño:** (Ej. MVC, Patrón de Repositorio).
- **Vistas de Alto Nivel:** Describir la arquitectura general (ej. 3 capas: Presentación, Lógica de Negocio y Datos) y su propósito (ej. mayor flexibilidad, seguridad y rendimiento).

### 5.2. Perspectiva Lógica (Vista de Casos de Uso)
- **Actores:** Definir los usuarios o sistemas que interactúan con la plataforma (Ej. Administrador, Cliente, Evaluador).
- **Casos de Uso Principales:** Listar y describir las funcionalidades más relevantes (Ej. Efectuar login, Gestión de usuarios, Gestión de reportes).
- **Diagrama:** Incluir el Diagrama de Casos de Uso que relacione los actores con las funcionalidades del sistema.

### 5.3. Perspectiva del Desarrollo (Despliegue e Implementación)
- **Diagrama de Despliegue:** Mostrar la agrupación de sistemas, sus comportamientos y las relaciones con el hardware (servidores).
- **Detalle del desarrollo (Diagrama Físico Relacional):** Referenciar al modelo de datos o esquema de la base de datos a implementar.

### 5.4. Requisitos de Calidad
[Describir cómo la arquitectura contribuye a las capacidades no funcionales del sistema.]
- **Seguridad:** (Ej. Autenticación, cifrado SSL/TLS).
- **Rendimiento/Escalabilidad:** (Ej. Arquitectura de 3 niveles para escalabilidad horizontal).
- **Disponibilidad:** (Ej. Estrategias de backup y redundancia).

## 6. Definiciones Técnicas
### 6.1. Lenguajes de Programación y Frameworks
- **Backend:** (Ej. NestJS / Node.js).
- **Frontend:** (Ej. Framework Angular).

### 6.2. Lenguaje de Programación de Base de Datos
- **Motor:** MariaDB.
- **Herramienta de Gestión:** PhpMyAdmin, MySQL Workbench.

### 6.3. Extensiones y Librerías Requeridas
[Listar todas las librerías o módulos específicos necesarios para el desarrollo.]

## 7. Requisitos de Hardware y Despliegue
| Componente                | Mínimo Requerido (Ejemplo)           |
|--------------------------|--------------------------------------|
| Servidor de Aplicaciones | 4GB RAM, 2 VCPU, 10 GB Almacenamiento|
| Servidor de Base de Datos| 2GB RAM, 10 GB Almacenamiento (MariaDB) |
| Sistema Operativo        | (Ej. Ubuntu 22.04.2 LTS)             |
| Servidor Web/Proxy       | (Ej. NGINX)                          |

## 8. Consideraciones
[Sección para incluir aspectos relevantes para la sostenibilidad y operación del sistema.]
- Requisitos de mantenimiento y actualización de software.
- Estrategia de backup y controles de seguridad.
- Necesidad de entornos de prueba (staging o gemelo) para validación de actualizaciones.
- Gestión del certificado de seguridad SSL.
