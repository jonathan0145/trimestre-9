# Plan de Pruebas y Rollback para la Migración de Datos

## 1. Objetivo
Establecer el plan de pruebas para validar la migración de datos y el procedimiento de rollback en caso de fallos críticos, asegurando la integridad y disponibilidad de la información.

## 2. Alcance
Incluye pruebas de migración de datos desde MySQL/phpMyAdmin al sistema destino, validaciones funcionales y técnicas, y el plan de contingencia para revertir la migración si es necesario.

## 3. Tipos de Pruebas

| Tipo de Prueba         | Descripción                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| Prueba de Volumen     | Migración de una muestra representativa de datos para validar tiempos y errores|
| Prueba de Integridad  | Verificación de que los datos migrados coinciden con el origen                |
| Prueba Funcional      | Validación de funcionalidades clave con los datos migrados                    |
| Prueba de Rendimiento | Medición de tiempos de consulta y carga tras la migración                     |
| Prueba de Rollback    | Simulación de fallo y ejecución del procedimiento de reversión                |

## 4. Cronograma de Pruebas y Ejecución

| Fase         | Tarea Específica                                              | Responsable         | Duración Estimada | Fecha de Finalización | Completado (Sí/No) |
|--------------|--------------------------------------------------------------|---------------------|-------------------|-----------------------|--------------------|
| Desarrollo   | Preparar scripts de migración y rollback                     | DBA                 | 2 días            | [Fecha]               |                    |
| Prueba 1     | Migrar muestra de datos y validar integridad                 | DBA/Usuarios Clave  | 1 día             | [Fecha]               |                    |
| Prueba 2     | Migración completa en entorno de pruebas                     | DBA/IT              | 2 días            | [Fecha]               |                    |
| Validación   | Validación funcional y técnica de los datos migrados         | Usuarios Clave      | 2 días            | [Fecha]               |                    |
| Rollback     | Simulación de rollback y restauración de backup              | DBA/IT              | 1 día             | [Fecha]               |                    |
| Go-Live      | Migración en producción y monitoreo                          | DBA/IT              | [Tiempo]          | [Fecha]               |                    |

## 5. Criterios de Aceptación
- Todos los datos críticos migrados correctamente y validados.
- No existen registros duplicados, incompletos o inconsistentes.
- Las funcionalidades clave operan correctamente con los datos migrados.
- El procedimiento de rollback se ejecuta correctamente en pruebas.

## 6. Plan de Contingencia (Rollback)

- Se realizará un backup completo del sistema destino antes de la migración final.
- Si se detectan errores críticos tras la migración:
	1. Detener el acceso al sistema destino.
	2. Restaurar el backup previo a la migración.
	3. Comunicar a los usuarios el retorno temporal al sistema anterior.
- Tiempo máximo de reversión estimado: 4 horas.

## 7. Checklist de Pruebas y Rollback
- [x] Preparar scripts de migración y rollback
- [x] Realizar pruebas de migración en entorno controlado
- [x] Validar integridad y funcionalidad de los datos migrados
- [x] Simular y documentar el procedimiento de rollback
- [x] Ejecutar migración final y monitoreo post-Go-Live
# Plan de Pruebas y Rollback

## Pruebas Técnicas
- Pruebas de rendimiento: medir tiempos de respuesta y carga.
- Pruebas de estrés: evaluar el sistema bajo alta demanda.
- Pruebas de seguridad: verificar protección de datos y accesos.

## Pruebas Funcionales
- Validación de funcionalidades clave por usuarios finales.
- Pruebas de integración entre módulos.

## Procedimiento de Rollback
- Definir puntos de restauración antes de cambios críticos.
- Planificar reversión rápida en caso de fallos graves.
- Documentar pasos y responsables del rollback.

---

**Checklist de pruebas y rollback:**
- [x] Definir escenarios de prueba
- [x] Ejecutar pruebas técnicas
- [x] Ejecutar pruebas funcionales
- [x] Registrar resultados y errores
- [x] Definir y probar procedimiento de rollback
- [x] Documentar incidencias y soluciones
