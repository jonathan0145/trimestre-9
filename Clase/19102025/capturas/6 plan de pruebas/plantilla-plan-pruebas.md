# PLANTILLA 6: PLAN DE PRUEBAS

**Propósito:** Definir el alcance, los objetivos, los recursos y los casos de prueba para asegurar que el nuevo sistema de software cumple con todos los requisitos funcionales y no funcionales antes del Go-Live.

**Proyecto:** [Nombre del Sistema a Implantar]
**Versión del Software a Probar:** [Número de Versión]
**Entorno de Prueba:** [Ej: QA, Pre-Producción]
**Fecha de Inicio de Pruebas:** [Día/Mes/Año]
**Fecha de Fin de Pruebas (Aprobación UAT):** [Día/Mes/Año]

---

## 1. Alcance y Tipos de Pruebas

### A. Objetivos de la Prueba
- Verificar que el sistema soporta todos los procesos de negocio definidos en el alcance.
- Asegurar que las integraciones con sistemas externos son sólidas y no generan errores de datos.
- Confirmar que el rendimiento del sistema es adecuado para el volumen de usuarios y transacciones esperados.
- Obtener la aprobación formal (Firma de UAT) de los usuarios clave.

### B. Tipos de Pruebas Incluidas

| Tipo de Prueba                | Descripción y Responsable                                                                 | Estado de Inclusión |
|-------------------------------|-----------------------------------------------------------------------------------------|---------------------|
| Pruebas Unitarias             | Validación del código a nivel de componente/módulo individual. (Desarrolladores)         | [Sí/No - Desarrolladores completadas] |
| Pruebas Funcionales           | Verificación de que cada función cumple el requisito del negocio. (Consultores Funcionales) | Sí                  |
| Pruebas de Integración        | Asegurar que el flujo de datos entre módulos o sistemas externos funciona. (IT/Usuarios Clave) | Sí                  |
| Pruebas de Rendimiento/Carga  | Medir la respuesta del sistema bajo picos de usuarios o transacciones. (IT/Proveedor)    | Sí                  |
| Pruebas UAT (Aceptación de Usuario) | Validación del sistema por usuarios finales en escenarios de negocio reales. (Usuarios Clave) | Sí                  |
| Pruebas de Seguridad          | Verificar permisos de acceso y protección de datos sensibles. (IT/Seguridad)             | Sí                  |

---

## 2. Estrategia de Prueba y Criterios de Aprobación

| Concepto                | Descripción / Definición                                                                 |
|-------------------------|----------------------------------------------------------------------------------------|
| Suspensión de Pruebas   | Si el [Porcentaje]% de los casos de prueba críticos (severidad Alta) fallan, la fase de pruebas se detiene hasta que se resuelvan los defectos. |
| Criterio de Inicio      | El entorno de prueba debe ser estable, los datos de prueba cargados y las configuraciones iniciales completadas. |
| Criterio de Finalización| El [Porcentaje]% de los casos de prueba debe ser aprobado. Ningún defecto de severidad Alta debe quedar abierto. |
| Defecto (Bug)           | Cualquier desviación del comportamiento esperado o del requisito documentado.            |

**Severidad de Defectos**

| Nivel         | Descripción                                                                 | Acción Requerida                                 |
|--------------|-----------------------------------------------------------------------------|-------------------------------------------------|
| Alta (Crítica)| Impide la ejecución de una función de negocio crítica. No hay solución temporal. | Resolución inmediata (Máx. 24 horas).           |
| Media        | El sistema funciona, pero con errores o con una solución alternativa complicada. | Resolución antes de la firma UAT.               |
| Baja         | Error cosmético, de formato o de documentación.                              | Resolución aplazable hasta la primera actualización (Patch). |

---

## 3. Matriz de Trazabilidad de Casos de Prueba (Ejemplo)
Este es el registro formal de la ejecución y el resultado de las pruebas.

| ID Caso      | Módulo      | Proceso a Probar         | Condiciones Iniciales         | Resultado Esperado         | Resultado Real             | Aprobado (Sí/No) | ID Defecto (si aplica) | Probador                |
|--------------|-------------|-------------------------|------------------------------|---------------------------|----------------------------|------------------|------------------------|-------------------------|
| CP-INV-001   | Inventario  | Recepción de Mercancía  | Existe Orden de Compra (OC). | El inventario aumenta en la cantidad recibida. | OK. Inventario actualizado. | Sí               | N/A                    | Juan Pérez (Logística)  |
| CP-FAC-005   | Facturación | Creación de Factura a Crédito | Cliente con límite de crédito superado. | El sistema debe emitir una advertencia, pero permitir la factura (override). | Fallo: El sistema bloquea completamente la factura. | No | D-FAC-012 | María López (Ventas) |
| CP-INT-010   | Integración | Transferencia de Pagos al ERP | Pago registrado en el nuevo sistema. | El asiento contable debe aparecer en el ERP en menos de 5 minutos. | OK. Asiento contable verificado. | Sí | N/A | Pedro Gómez (IT) |
| CP-PERF-001  | Rendimiento | Consulta Masiva de Reporte | 100 usuarios concurrentes. | Tiempo de respuesta del reporte | 6.5 segundos. | No | D-PERF-001 | Equipo IT            |
| CP-XXX-XXX   |             |                         |                              | < 5 segundos.              |                            |                  |                        |                         |

---

## 4. Registro de Defectos (Defect Log)
Registro y seguimiento de los errores encontrados durante la ejecución de las pruebas.

| ID Defecto   | Fecha Reporte | Descripción del Defecto | Módulo Afectado | Severidad | Responsable de la Corrección | Fecha Estimada de Solución | Estado                |
|--------------|---------------|------------------------|-----------------|-----------|-----------------------------|---------------------------|-----------------------|
| D-FAC-012    | 05/06/2025    | El sistema bloquea las facturas a crédito si el límite se supera (el requisito es advertencia). | Facturación | Alta | Desarrollador A | 08/06/2025 | [Abierto/Resuelto/Cerrado] |
| D-PERF-001   | 06/06/2025    | El reporte de Inventario tarda demasiado al ejecutarse con 100 usuarios. | Base de Datos | Alta | DBA | 10/06/2025 | [Abierto/Resuelto/Cerrado] |
| D-INV-003    | 07/06/2025    | El logo de la empresa en el reporte de Stock aparece pixelado. | UI/Reporte | Baja | Desarrollador B | 15/06/2025 | [Abierto/Resuelto/Cerrado] |
| D-XXX-XXX    |               |                        |                 |           |                             |                           |                       |
