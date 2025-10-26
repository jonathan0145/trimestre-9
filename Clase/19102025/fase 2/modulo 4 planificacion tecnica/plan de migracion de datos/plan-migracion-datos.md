# Plan de Migración de Datos

Propósito: Establecer la estrategia, procedimientos, responsables y cronograma para la extracción, limpieza, transformación y carga (ETL) de los datos desde phpMyAdmin (MySQL) al sistema destino.

Proyecto: [Nombre del Nuevo Sistema]
Sistema Fuente (Actual): MySQL (phpMyAdmin)
Sistema Destino (Nuevo): [Nombre y Versión]
Fecha de Corte de Datos (Go-Live): [Día/Mes/Año]

## 1. Alcance y Estrategia de Migración

| Concepto                | Descripción Detallada                                                                 |
|-------------------------|-------------------------------------------------------------------------------------|
| Tipo de Migración       | Reemplazo Total (toda la base de datos actual será migrada al nuevo sistema)         |
| Datos a Migrar          | Tablas principales: usuarios, productos, ventas, historial, etc.                     |
| Datos Excluidos         | Logs temporales, registros de pruebas, datos de usuarios inactivos >3 años           |
| Herramienta de Migración| Exportación/Importación SQL desde phpMyAdmin, scripts personalizados (Python/SQL)    |
| Equipo Responsable      | DBA, Especialista en Datos, Usuarios Clave                                           |

## 2. Inventario de Origen y Destino de Datos

| Categoría de Dato      | Tabla/Campo en el Sistema Fuente | Tabla/Campo en el Sistema Destino | Regla de Transformación                        | Volumen Estimado | Responsable         |
|-----------------------|----------------------------------|-----------------------------------|------------------------------------------------|------------------|---------------------|
| Usuarios              | users.id, users.email            | users.id, users.email             | Mapeo directo 1:1                              | 2,000            | Usuarios Clave      |
| Productos             | products.sku                     | products.sku                      | Completar con ceros a la izquierda (8 dígitos) | 1,000            | Consultor Funcional |
| Ventas                | sales.date, sales.amount         | sales.date, sales.amount          | Solo ventas de los últimos 2 años               | 10,000           | Finanzas            |
| Historial             | history.event, history.date      | history.event, history.date       | Excluir eventos de prueba                       | 50,000           | IT                  |

## 3. Plan de Limpieza y Calidad de Datos

| Problema de Calidad   | Acción Correctiva (Limpieza)                                  | Responsable         | Fecha de Finalización | Aprobación de la Limpieza |
|----------------------|----------------------------------------------------------------|---------------------|-----------------------|--------------------------|
| Registros Duplicados | Identificar y consolidar usuarios con el mismo correo           | Usuarios Clave      | [Fecha]               | [Aprobador]              |
| Datos Incompletos    | Completar campos obligatorios (ej. dirección, email)            | Usuarios Clave      | [Fecha]               | [Aprobador]              |
| Formatos Incorrectos | Validar formatos de fecha y numéricos (YYYY-MM-DD, decimales)   | DBA                 | [Fecha]               | [Aprobador]              |
| Datos Obsoletos      | Eliminar registros de pruebas y usuarios inactivos >3 años      | IT                  | [Fecha]               | [Aprobador]              |

## 4. Cronograma de Pruebas y Ejecución de Migración (ETL)

| Fase         | Tarea Específica                                              | Responsable         | Duración Estimada | Fecha de Finalización | Completado (Sí/No) |
|--------------|--------------------------------------------------------------|---------------------|-------------------|-----------------------|--------------------|
| Desarrollo   | Exportar datos desde phpMyAdmin y preparar scripts de carga  | DBA                 | 2 días            | [Fecha]               |                    |
| Prueba 1     | Migrar muestra de datos a entorno de pruebas                 | DBA                 | 1 día             | [Fecha]               |                    |
| Validación 1 | Validar integridad y funcionalidad de la muestra migrada     | Usuarios Clave      | 1 día             | [Fecha]               |                    |
| Prueba 2     | Migración completa en entorno pre-producción                 | DBA/IT              | 2 días            | [Fecha]               |                    |
| Validación 2 | Validación final y conciliación de datos                     | Finanzas/Usuarios   | 2 días            | [Fecha]               |                    |
| Go-Live      | Migración en producción (Go-Live)                            | DBA/IT              | [Tiempo]           | [Fecha]               |                    |

## 5. Plan de Contingencia (Rollback)

- Se realizará un backup completo del sistema destino antes de la carga final.
- Si la migración falla o se detectan errores críticos en las primeras horas:
	1. Detener el nuevo sistema.
	2. Restaurar el backup previo a la migración.
	3. Comunicar el retorno temporal al sistema anterior a los usuarios.
- Tiempo máximo de reversión estimado: 4 horas.

---

**Checklist de migración de datos:**
- [x] Definir estrategia de migración
- [x] Identificar fuentes y destino de datos
- [x] Realizar limpieza de datos
- [x] Validar integridad y calidad
- [x] Ejecutar migración
- [x] Verificar resultados y corregir errores
- [x] Documentar incidencias y soluciones
