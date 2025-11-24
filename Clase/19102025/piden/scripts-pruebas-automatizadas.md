# Scripts de Prueba Automatizados

## Propósito
Este documento describe los scripts de prueba automatizados implementados para validar la funcionalidad, calidad y estabilidad del sistema. Incluye detalles sobre las herramientas utilizadas, el alcance de las pruebas, la estructura de los scripts y evidencia de ejecución.

## Herramientas Utilizadas
- **Jest**: Pruebas unitarias e integración para backend Node.js.
- **Selenium**: Pruebas de interfaz gráfica y flujo de usuario.
- **JUnit**: Pruebas unitarias para componentes Java (si aplica).
- Otras herramientas relevantes: [especificar si aplica]

## Alcance de las Pruebas
- Pruebas unitarias: Validan funciones y métodos individuales.
- Pruebas de integración: Verifican la interacción entre módulos y servicios (por ejemplo, HTTP + WebSocket).
- Pruebas end-to-end (E2E): Simulan escenarios completos de usuario.
- Pruebas de regresión: Aseguran que nuevas funcionalidades no rompen el sistema existente.

## Estructura de los Scripts
- Ubicación de los scripts: `backend/test/`, `frontend/test/`, etc.
- Convención de nombres: `*.test.js`, `*.spec.js`, etc.
- Ejemplo de estructura:
  ```
  backend/test/integration/socket.e2e.test.js
  frontend/test/e2e/login.test.js
  ```

## Ejecución de Pruebas
- Comando para ejecutar pruebas backend:
  ```bash
  NODE_ENV=test npx jest --runInBand --detectOpenHandles
  ```
- Comando para ejecutar pruebas Selenium:
  ```bash
  npx selenium-side-runner tests/*.side
  ```
- Comando para ejecutar pruebas JUnit:
  ```bash
  mvn test
  ```
- [Agregar comandos específicos según el stack]

## Evidencia de Ejecución
- Reporte de resultados: logs, capturas de pantalla, archivos de salida (`test-results.xml`, `coverage/`, etc.).
- Ejemplo de salida:
  ```
  PASS backend/test/integration/socket.e2e.test.js
  PASS frontend/test/e2e/login.test.js
  ```
- Ubicación de los reportes: `piden/`, `backend/test/results/`, etc.

## Mantenimiento y Actualización
- Frecuencia de ejecución: [diaria/semanal/despliegue]
- Responsable: [nombre del responsable]
- Procedimiento para agregar nuevos scripts: [describir brevemente]

## Referencias
- Manual de Usuario
- Especificación de Casos de Prueba
- Documentación Técnica

---

> **Nota:** Este documento debe actualizarse cada vez que se agreguen, modifiquen o eliminen scripts de prueba automatizados. Incluya evidencia de ejecución reciente y asegúrese de que los comandos y rutas sean correctos para el entorno actual.

---

## Plantilla para Registro de Scripts de Pruebas Automatizadas

A continuación se presenta una plantilla para documentar cada nuevo script de prueba automatizada que se agregue al proyecto. Copie y complete esta sección por cada script nuevo:

---
### Nombre del Script:
`[nombre-del-script.test.js]`

**Ubicación:** `[ruta/relativa/al/script]`

**Tipo de Prueba:** `[unitaria | integración | E2E | regresión]`

**Descripción:**
Breve explicación del objetivo del script y qué funcionalidad valida.

**Herramienta Utilizada:** `[Jest | Selenium | JUnit | Otra]`

**Comando de Ejecución:**
```bash
[comando para ejecutar la prueba]
```

**Evidencia de Ejecución:**
- Resultado esperado: `[PASS/FAIL]`
- Archivos generados: `[logs, capturas, reportes, etc.]`
- Ubicación de la evidencia: `[ruta/relativa/a/evidencia]`

**Responsable:** `[nombre del responsable]`

---

> Complete una sección como esta por cada script nuevo para mantener la trazabilidad y el control de calidad en el proyecto.

## Ejemplos documentados de scripts de prueba automatizada

---
### Nombre del Script:
`user.test.js`

**Ubicación:** `backend/tests/user.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de usuarios: consulta de lista y creación de usuario.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/user.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.00 PM (1)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.00%20PM%20(1).jpeg)
- Ubicación de la evidencia: `backend/tests/results/` (si se configura)

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`role.test.js`

**Ubicación:** `backend/tests/role.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de roles: consulta de lista y creación de rol.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/role.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.00 PM (2)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.00%20PM%20(2).jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`verification.test.js`

**Ubicación:** `backend/tests/verification.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de verificaciones: consulta de lista y creación de verificación.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/verification.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.00 PM](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.00%20PM.jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`appointment.test.js`

**Ubicación:** `backend/tests/appointment.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de citas: consulta de lista y creación de cita.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/appointment.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.01 PM (1)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.01%20PM%20(1).jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`chat.test.js`

**Ubicación:** `backend/tests/chat.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de chats: consulta de lista y creación de chat.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/chat.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.01 PM (2)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.01%20PM%20(2).jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`offer.test.js`

**Ubicación:** `backend/tests/offer.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de ofertas: consulta de lista y creación de oferta.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/offer.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.01 PM (3)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.01%20PM%20(3).jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`property.test.js`

**Ubicación:** `backend/tests/property.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de propiedades: consulta de lista y creación de propiedad.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/property.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.01 PM (4)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.01%20PM%20(4).jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`notification.test.js`

**Ubicación:** `backend/tests/notification.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de notificaciones: consulta de lista y creación de notificación.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/notification.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.01 PM copy](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.01%20PM%20copy.jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`priceHistory.test.js`

**Ubicación:** `backend/tests/priceHistory.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de historial de precios: consulta de lista y creación de registro.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/priceHistory.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.02 PM (1)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.02%20PM%20(1).jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`file.test.js`

**Ubicación:** `backend/tests/file.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida los endpoints de archivos: consulta de lista y subida de archivo.

**Herramienta Utilizada:** Jest + Supertest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/file.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.02 PM (2)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.02%20PM%20(2).jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`conversationUtils.test.js`

**Ubicación:** `backend/tests/conversationUtils.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida la función utilitaria `userBelongsToConversation` y su comportamiento con datos simulados y reales en la base de datos.

**Herramienta Utilizada:** Jest

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/tests/conversationUtils.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.03 PM (1)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.03%20PM%20(1).jpeg)
- Ubicación de la evidencia: `backend/tests/results/`

**Responsable:** jonathan ivan rendon bermeo

---
### Nombre del Script:
`socket.test.js`

**Ubicación:** `backend/test/integration/socket.test.js`

**Tipo de Prueba:** integración

**Descripción:**
Valida el flujo de autenticación y eventos en el sistema de sockets, incluyendo registro, login, creación de propiedad y comunicación por WebSocket.

**Herramienta Utilizada:** Jest + Supertest + Socket.io-client

**Comando de Ejecución:**
```bash
NODE_ENV=test npx jest --runInBand --detectOpenHandles backend/test/integration/socket.test.js
```

**Evidencia de Ejecución:**
- Resultado esperado: `PASS`
- Archivos generados: logs de consola, reportes de Jest, capturas de pantalla
- Ejemplo de evidencia:
  ![WhatsApp Image 2025-11-23 at 5.26.04 PM (1)](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.04%20PM%20(1).jpeg)
- Ubicación de la evidencia: `backend/test/integration/results/`

**Responsable:** jonathan ivan rendon bermeo

---

## Evidencia final: Todos los tests pasan

![Todos los tests pasan](./imagenes%20test/WhatsApp%20Image%202025-11-23%20at%205.26.05%20PM.jpeg)

---
