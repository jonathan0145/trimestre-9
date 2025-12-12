# Plantilla: Documento de Arquitectura del Sistema (DAS)

**Título del Proyecto:** INMOTECH

**Versión:** 1.0

**Fecha:** 15/11/2025

**Elaborado por:** Nadia Masmela, Anderson Mora y Jonathan Ivan Rendon Bermeo

## Diagrama de Casos de Uso

![Diagrama de Casos de Uso](https://docs.google.com/document/d/11YKno4Yn1_MqPRMn5ydDyl6VuiVxj_hc/edit)

*El diagrama muestra los principales actores (usuarios, agentes, administradores) y los flujos clave: registro, autenticación, gestión de propiedades, inicio de conversaciones, envío de mensajes, gestión de archivos y notificaciones.*

## 0. Historial de Versiones
| Versión | Fecha  | Autor  | Descripción del Cambio         |
|---------|--------|--------|-------------------------------|
| 1.0     | 15/11/2025| Nadia Masmela, Anderson Mora y Jonathan Ivan Rendon Bermeo| Creación del documento        |

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
INMOTECH es una plataforma web orientada a la gestión integral de propiedades inmobiliarias, agentes y usuarios finales. El sistema está diseñado para digitalizar y automatizar procesos que tradicionalmente se realizan de forma manual, como la administración de ofertas, la comunicación entre usuarios y agentes, y la verificación de información. La solución responde a la necesidad de modernizar la gestión inmobiliaria, mejorar la seguridad y trazabilidad de los datos, y facilitar la interacción en tiempo real entre los diferentes actores del sector.

## 2. Problema
Actualmente, la gestión de propiedades, ofertas y usuarios en el sector inmobiliario se realiza en gran parte de manera manual o mediante herramientas ofimáticas dispersas, lo que genera inconsistencias, duplicidad de información y problemas de seguridad. La falta de integración y automatización dificulta el seguimiento de procesos, la verificación de datos y la comunicación eficiente entre agentes y clientes. Además, existen riesgos asociados al manejo de información sensible sin mecanismos robustos de autenticación y protección.

## 3. Solución Propuesta
Se propone el desarrollo de una plataforma web modular compuesta por un backend en Node.js (Express, Sequelize, Socket.io) y un frontend en React. El sistema implementa una arquitectura de capas, con API RESTful y servicios de sockets para comunicación en tiempo real, y una base de datos relacional (MySQL/MariaDB). La solución integra autenticación segura (JWT, Firebase), gestión de roles y permisos, y automatización de procesos clave como la verificación de usuarios y propiedades, la administración de ofertas y la comunicación instantánea. Los principales beneficios incluyen la sistematización de la información, la mejora en la seguridad, la trazabilidad de los procesos y la escalabilidad para soportar alta concurrencia.

## 4. Objetivos
### 4.1. Objetivo general
Desarrollar e implementar una plataforma web integral para la gestión inmobiliaria, que permita la administración segura y eficiente de propiedades, usuarios, agentes y ofertas, facilitando la comunicación y la verificación de información en tiempo real.

### 4.2. Objetivos específicos
1. Digitalizar y automatizar el proceso de registro, gestión y verificación de usuarios, agentes y propiedades.
2. Implementar mecanismos de autenticación y autorización robustos, incluyendo roles y permisos diferenciados.
3. Facilitar la comunicación en tiempo real entre usuarios y agentes mediante servicios de chat y notificaciones.
4. Integrar la gestión de ofertas y transacciones inmobiliarias con trazabilidad y control de cambios.
5. Garantizar la seguridad y protección de datos sensibles mediante cifrado y buenas prácticas de desarrollo.
6. Proveer herramientas para el monitoreo, análisis y reporte de la calidad del software (ISO 25010, SonarQube, checklist de calidad).
7. Asegurar la escalabilidad y disponibilidad del sistema para soportar alta concurrencia y crecimiento futuro.

## 5. Propuesta Arquitectónica
El objeto de esta sección es sustentar la solución propuesta desde la arquitectura técnica, sirviendo como referencia para el diseño y análisis de la implementación.


### 5.1. Definición y Estilo de la Arquitectura
- **Estilo Principal:** Arquitectura de capas (Backend, Frontend, Base de datos).
- **Patrones de Diseño:** MVC en backend, modularidad en frontend React.
- **Vistas de Alto Nivel:**
   - Backend Node.js expone API RESTful y servicios de sockets.
   - Frontend React consume la API y los sockets en tiempo real.
   - Base de datos relacional (MariaDB/SQLite para pruebas).
   - Integración con servicios externos (Firebase para autenticación y notificaciones).


### 5.2. Perspectiva Lógica (Vista de Casos de Uso)
- **Actores:** Administrador, Agente, Usuario final.
- **Casos de Uso Principales:**
   - Autenticación y gestión de usuarios
   - Gestión de propiedades y ofertas
   - Chat y notificaciones en tiempo real
   - Verificación de usuarios y propiedades
   - Administración de roles y permisos
- **Diagrama:** (Incluir diagrama de casos de uso con los actores y funcionalidades principales).


### 5.3. Perspectiva del Desarrollo (Despliegue e Implementación)
- **Diagrama de Despliegue:**
   - Backend y frontend desplegados en servidor Debian-web-server.
   - Base de datos MariaDB en el mismo servidor o instancia separada, gestionada por Nadia Masmela, Anderson Mora y Jonathan Ivan Rendon Bermeo.
   - Acceso externo mediante NGINX como proxy reverso.
- **Detalle del desarrollo:**
   - Modelo físico relacional: tablas para usuarios, propiedades, ofertas, mensajes, notificaciones, roles y permisos.


### 5.4. Requisitos de Calidad
- **Seguridad:**
   - Autenticación JWT, cifrado de contraseñas, uso de HTTPS (SSL/TLS).
   - Integración con Firebase para doble factor y notificaciones seguras.
- **Rendimiento/Escalabilidad:**
   - Backend desacoplado del frontend, escalable horizontalmente.
   - Uso de sockets para comunicación en tiempo real.
- **Disponibilidad:**
   - Estrategias de backup automáticas, redundancia en base de datos y archivos.
   - Monitoreo y alertas en tiempo real.

## 6. Definiciones Técnicas

### 6.1. Lenguajes de Programación y Frameworks
- **Backend:** Node.js (Express, Sequelize, Socket.io)
- **Frontend:** React.js (con Webpack)
- **Scripts y automatización:** JavaScript (Node), Shell


### 6.2. Lenguaje de Programación de Base de Datos
- **Motor:** MariaDB (producción), SQLite (pruebas)
- **Herramienta de Gestión:** DB Browser for SQLite, MySQL Workbench


### 6.3. Extensiones y Librerías Requeridas
- **Backend:**
   - express (^4.21.2)
   - sequelize (^6.37.7)
   - socket.io (^4.7.5)
   - firebase-admin (^13.5.0)
   - jest (^29.6.1)
   - supertest (^6.3.3)
   - dotenv (^16.5.0)
   - mysql2 (^3.14.1)
   - sqlite3 (^5.1.7)
- **Frontend:**
   - react (19.0.0)
   - axios (^1.8.4)
   - socket.io-client (^4.7.5)
   - redux (^5.0.1)
   - webpack (5.97.1)
   - jest (no configurado en frontend)
   - @testing-library/react (no especificado en frontend)


## 7. Requisitos de Hardware y Despliegue
| Componente                | Requerido para alta concurrencia       |
|--------------------------|----------------------------------------|
| Servidor de Aplicaciones | 128GB RAM, 64 VCPU, 10 TB Almacenamiento |
| Servidor de Base de Datos| 256GB RAM, 32 VCPU, 20 TB Almacenamiento |
| Sistema Operativo        | Debian-web-server                      |
| Servidor Web/Proxy       | NGINX                                  |


## 8. Consideraciones
- Mantenimiento y actualización periódica de dependencias.
- Estrategia de backup diario y controles de seguridad.
- Entorno de pruebas (staging) para validación de actualizaciones.
- Certificado SSL para acceso seguro.
