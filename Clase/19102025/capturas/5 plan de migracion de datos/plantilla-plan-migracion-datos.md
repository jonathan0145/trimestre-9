# PLANTILLA 5: PLAN DE MIGRACIÓN DE DATOS

**Propósito:** Establecer la estrategia, los procedimientos, las responsabilidades y el cronograma para la extracción, limpieza, transformación y carga (ETL) de los datos del sistema fuente al sistema destino.

**Proyecto:** [Nombre del Nuevo Sistema a Implantar]
**Sistema Fuente (Actual):** [Nombre y Versión]
**Sistema Destino (Nuevo):** MariaDB [Versión]
**Fecha de Corte de Datos (Go-Live):** [Día/Mes/Año]

---

## 1. Alcance y Estrategia de Migración

| Concepto              | Descripción Detallada                                                                 |
|-----------------------|-------------------------------------------------------------------------------------|
| Tipo de Migración     | [Ej: Reemplazo Total / Coexistencia (Migración parcial) / Historial solo a demanda]  |
| Datos a Migrar        | [Especificar: Maestros, Saldos de Apertura, Histórico de X años, etc.]               |
| Datos Excluidos       | [Ej: Transacciones de más de 5 años, datos de clientes inactivos sin movimiento en 3 años.] |
| Herramienta de Migración | [Ej: Scripts SQL / Herramienta ETL dedicada (SSIS, Talend) / Funcionalidad nativa del nuevo sistema.] |
| Equipo Responsable    | Nadia Masmela, Anderson Mora, Jonathan Ivan Rendon Bermeo |

---

## 2. Inventario de Origen y Destino de Datos
Esta matriz detalla los elementos clave a migrar y la forma en que se mapearán entre los dos sistemas.

| Categoría de Dato      | Tabla/Campo en el Sistema Fuente | Tabla/Campo en el Sistema Destino | Regla de Transformación | Volumen Estimado (Registros) | Responsable      |
|------------------------|----------------------------------|-----------------------------------|------------------------|------------------------------|------------------|
| Maestros de Clientes   | Clientes.CodCliente              | CUST_MASTER.CUSTOMER_ID           | Mapeo directo 1:1.     | [Ej: 50,000]                 | Nadia Masmela   |
| Maestros de Productos  | Inventario.SKU                   | PRODUCT.PRODUCT_CODE              | El campo debe ser rellenado con ceros iniciales hasta 8 dígitos. | [Ej: 10,500] | Anderson Mora |
| Saldos de Cuentas      | Contabilidad.Saldo               | GL_ACCOUNT.BALANCE                | Sólo el saldo final al día de corte. Requiere conciliación con fuente. | [Ej: 300] | Jonathan Ivan Rendon Bermeo |
| Histórico de Pedidos   | Pedidos.Fecha                    | SALES_ORDER.ORDER_DATE             | Excluido (Sólo migrar pedidos de los últimos 6 meses). | [Ej: 200,000] | Anderson Mora |
| [Añadir otra categoría]|                                  |                                   |                        |                              |                  |

---

## 3. Plan de Limpieza y Calidad de Datos
Acciones específicas para asegurar la calidad de la información antes de la carga final.

| Problema de Calidad    | Acción Correctiva (Limpieza)                                  | Responsable      | Fecha de Finalización | Aprobación de la Limpieza |
|------------------------|---------------------------------------------------------------|------------------|----------------------|--------------------------|
| Registros Duplicados   | Identificar y consolidar registros de clientes con el mismo CIF/RUC. | Nadia Masmela   |                      |                          |
| Datos Incompletos      | Rellenar el campo "Dirección" obligatorio para todos los registros activos. | Anderson Mora   |                      |                          |
| Homologación de Unidades | Normalizar las unidades de medida (Ej: de "CAJA" a "CX", de "Litro" a "LT"). | Jonathan Ivan Rendon Bermeo |                  |                          |
| Datos Obsoletos        | Archivar y marcar como inactivos los registros de proveedores sin movimiento en 2 años. | Anderson Mora |                  |                          |

---

## 4. Cronograma de Pruebas y Ejecución de Migración (ETL)
El plan debe incluir al menos una prueba de migración completa antes de la ejecución final.

| Fase         | Tarea Específica                                                      | Responsable           | Duración Estimada | Fecha de Finalización | Completado (Sí/No) |
|--------------|-----------------------------------------------------------------------|-----------------------|-------------------|----------------------|--------------------|
| Desarrollo   | Desarrollo de los scripts/mapeos ETL para MariaDB.                                 | Jonathan Ivan Rendon Bermeo | 2 Semanas         |                      |                    |
| Prueba 1 (Ambiente QA) | Ejecución de la migración de una muestra de datos (Test de Volumen bajo) en MariaDB. | Anderson Mora                  | 1 Día             |                      |                    |
| Validación 1 | Validación funcional y conciliación de la muestra migrada por Nadia Masmela. | Nadia Masmela       | 2 Días            |                      |                    |
| Prueba 2 (Ambiente Pre-Prod) | SIMULACRO COMPLETO: Migración de todos los datos en volumen total en MariaDB. | Jonathan Ivan Rendon Bermeo                  | 3 Días            |                      |                    |
| Validación Final | Conciliación y aprobación final de saldos y registros por Nadia Masmela. | Nadia Masmela          | 5 Días            |                      |                    |
| Ejecución Final | MIGRACIÓN EN PRODUCCIÓN (GO-LIVE) en MariaDB.                                 | Anderson Mora               | [Tiempo de Inactividad] |                  |                    |

---

## 5. Plan de Contingencia (Rollback)
Estrategia para revertir la migración en caso de un fallo catastrófico en la ejecución final.

- **Punto de Recuperación:** Se tomará una copia de seguridad completa del sistema destino inmediatamente antes de iniciar la carga final de datos.
- **Procedimiento de Desactivación:** Si la migración falla o se detectan errores críticos dentro de las primeras [Número] horas de Go-Live, se procederá a:
  1. Detener inmediatamente el nuevo sistema.
  2. Restaurar la copia de seguridad del sistema destino al estado previo a la carga.
  3. Comunicar el fallo y el retorno temporal al sistema fuente a todos los usuarios.
  4. [Añadir otro paso específico.]
- **Tiempo Máximo de Reversión Estimado:** [Ej: 4 horas].
