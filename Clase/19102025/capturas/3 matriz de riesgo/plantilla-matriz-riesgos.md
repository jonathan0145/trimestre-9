# PLANTILLA 3: MATRIZ DE RIESGOS

**Propósito:** Identificar, evaluar, priorizar y establecer planes de mitigación para los riesgos inherentes al proyecto de implantación del nuevo software.

**Proyecto:** [Nombre del Sistema a Implantar]
**Fecha de Elaboración:** [Día/Mes/Año]
**Responsable de la Matriz:** [Nombre y Cargo]
**Umbral de Riesgo Aceptable:** [Ej: Todo riesgo con "Nivel de Riesgo" Alto debe tener un plan de respuesta inmediato.]

---

## 1. Escala de Evaluación de Riesgos
Para garantizar la uniformidad en la evaluación, utilizaremos las siguientes escalas:

### A. Escala de Probabilidad (P)
| Nivel | Descripción                                      | Valor |
|-------|--------------------------------------------------|-------|
| Baja  | Es muy improbable que ocurra (Menos del 10%).    | 1     |
| Media | Podría ocurrir, pero no es lo más esperado (10%-50%). | 2     |
| Alta  | Es muy probable que ocurra (Más del 50%).        | 3     |

### B. Escala de Impacto (I)
| Nivel | Descripción de la Consecuencia                                                                 | Valor |
|-------|-----------------------------------------------------------------------------------------------|-------|
| Bajo  | Afecta mínimamente el cronograma o el presupuesto. No afecta la funcionalidad central.        | 1     |
| Medio | Retraso significativo (Semanas) o sobrecoste moderado. Afecta funcionalidad no crítica.       | 2     |
| Alto  | Detiene el proyecto o exige cambios fundamentales en el alcance. Pérdida crítica de datos o funcionalidad. | 3     |

### C. Nivel de Riesgo (NR)
SSNR = P x I

| Resultado | Nivel de Riesgo | Acción Requerida                                                                 |
|-----------|-----------------|---------------------------------------------------------------------------------|
| 1 - 2     | Bajo            | Monitorear y gestionar de forma rutinaria.                                       |
| 3 - 4     | Medio           | Asignar un responsable y definir un plan de mitigación.                         |
| 6 - 9     | Alto            | Asignar recursos inmediatos, monitoreo semanal y definir un plan de contingencia.|

---

## 2. Matriz Detallada de Riesgos del Proyecto

| ID  | Riesgo Identificado                        | Causa/Fuente Potencial                        | P | I | NR (P x I) | Estrategia de Respuesta | Plan de Mitigación/Acción                                      | Responsable         | Estado            |
|-----|--------------------------------------------|-----------------------------------------------|---|---|------------|------------------------|----------------------------------------------------------------|---------------------|-------------------|
| R01 | Retraso de la migración de datos.          | Falta de limpieza de datos históricos o incompatibilidad de formatos. | 3 | 3 | 9 (Alto)   | Mitigar                | Realizar al menos dos simulaciones completas de migración antes del Go-Live. | Líder de IT        | [Abierto/Cerrado] |
| R02 | Resistencia al cambio del personal clave.  | Miedo a perder el control o la familiaridad con el sistema actual.    | 3 | 2 | 6 (Alto)   | Mitigar                | Identificar “Súper Usuarios” y darles capacitación avanzada e incentivos.    | RR.HH./GP          | [Abierto/Cerrado] |
| R03 | Falla de la integración con el sistema financiero (ERP). | Cambios no documentados en la API del sistema financiero. | 2 | 3 | 6 (Alto)   | Mitigar                | Realizar pruebas de carga y estrés en el entorno de pruebas.                  | Desarrollador      | [Abierto/Cerrado] |
| R04 | Falta de disponibilidad de usuarios clave para capacitación. | Alta carga de trabajo en el cierre de mes o temporada alta. | 3 | 1 | 3 (Medio)  | Aceptar                | Programar las capacitaciones fuera del horario pico o en bloques de 2 horas. | Capacitación       | [Abierto/Cerrado] |
| R05 | Fallo del proveedor en la entrega de módulos a tiempo. | Problemas internos de personal del proveedor o sobrecarga de proyectos. | 1 | 3 | 3 (Medio)  | Transferir              | Incluir cláusulas de penalización por retrasos en el contrato de servicio.    | Gerencia           | [Abierto/Cerrado] |
| R06 | Hardware de servidores insuficiente para el nuevo sistema. | Especificación técnica subestimada en la fase inicial. | 2 | 2 | 4 (Medio)  | Mitigar                | Realizar una auditoría de infraestructura y verificar el dimensionamiento (sizing) antes de la instalación. | IT                  | [Abierto/Cerrado] |
| R07 |                                              |                                               |   |   |            |                        |                                                                |                     |                   |
| R08 |                                              |                                               |   |   |            |                        |                                                                |                     |                   |

---

## 3. Registro de Incidencias/Planes de Contingencia
Esta sección se utiliza para registrar los riesgos que se materializaron o para detallar la respuesta a los riesgos de Alto nivel.

| ID Riesgo | Incidencia Materializada (Fecha) | Plan de Contingencia (Acción detallada) | Costo/Impacto Real | Fecha de Resolución |
|-----------|----------------------------------|------------------------------------------|--------------------|---------------------|
| R01       | 15/05/2025: Se encontraron 10% de registros inconsistentes. | Se activó el equipo de "limpieza de datos" con un presupuesto extra de 80 horas. | Retraso de 1 semana en la Fase III. | 22/05/2025          |
| R03       | 01/06/2025: Se perdió el 2% de las transacciones en la prueba de estrés. | Se revisaron y optimizaron los timeouts de la integración y se aumentó la capacidad del servidor de colas. | Costo extra de $1,500 en licencias temporales. | 08/06/2025          |
| Rxx       |                                  |                                          |                    |                     |
