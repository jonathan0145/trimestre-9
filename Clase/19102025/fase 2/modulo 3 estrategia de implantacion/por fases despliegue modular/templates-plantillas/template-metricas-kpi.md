# Template - Métricas y KPIs por Fase

## 📋 Información del Proyecto
- **Nombre del Proyecto:** InmoTech - Sistema de Gestión Inmobiliaria
- **Fase:** [ESPECIFICAR_FASE]
- **Fecha de Inicio:** [DD/MM/AAAA]
- **Fecha de Fin:** [DD/MM/AAAA]
- **Responsable de Métricas:** [NOMBRE_RESPONSABLE]
- **Versión del Template:** 1.0

---

## 🎯 Objetivos de Medición

### Objetivo Principal
Establecer un sistema de métricas y KPIs que permita monitorear el progreso, calidad y éxito de cada fase del proyecto de manera objetiva y medible.

### Objetivos Específicos
- [ ] Monitorear el progreso del proyecto en tiempo real
- [ ] Identificar desviaciones tempranamente
- [ ] Evaluar la calidad de los entregables
- [ ] Medir la satisfacción de stakeholders
- [ ] Optimizar recursos y procesos continuamente

---

## 📊 Dashboard Ejecutivo - KPIs Principales

### 🚀 Métricas de Progreso General

#### Avance de la Fase (%)
- **Definición:** Porcentaje de tareas completadas vs planificadas
- **Objetivo:** ≥ 95% al finalizar la fase
- **Frecuencia:** Diaria
- **Fórmula:** (Tareas Completadas / Tareas Planificadas) × 100
- **Semáforo:**
  - 🟢 Verde: ≥ 90%
  - 🟡 Amarillo: 75-89%
  - 🔴 Rojo: < 75%

#### Cumplimiento de Cronograma (%)
- **Definición:** Porcentaje de hitos entregados a tiempo
- **Objetivo:** ≥ 90%
- **Frecuencia:** Semanal
- **Fórmula:** (Hitos A Tiempo / Total Hitos) × 100
- **Medición Actual:** [VALOR_ACTUAL]%

#### Desviación de Presupuesto (%)
- **Definición:** Diferencia entre presupuesto planificado y ejecutado
- **Objetivo:** ± 5%
- **Frecuencia:** Semanal
- **Fórmula:** ((Costo Real - Costo Planificado) / Costo Planificado) × 100
- **Medición Actual:** [VALOR_ACTUAL]%

### 💰 Métricas Financieras

#### Costo por Funcionalidad Implementada
- **Definición:** Costo promedio de implementar cada funcionalidad
- **Objetivo:** ≤ [VALOR_OBJETIVO] por funcionalidad
- **Frecuencia:** Al completar funcionalidad
- **Fórmula:** Costo Total de la Fase / Número de Funcionalidades
- **Medición Actual:** $[VALOR_ACTUAL]

#### ROI Parcial de la Fase
- **Definición:** Retorno de inversión estimado de la fase
- **Objetivo:** ≥ 15% anual
- **Frecuencia:** Mensual
- **Fórmula:** (Beneficios Esperados - Costo de Implementación) / Costo × 100
- **Medición Actual:** [VALOR_ACTUAL]%

#### Burn Rate (Velocidad de Gasto)
- **Definición:** Velocidad de consumo del presupuesto
- **Objetivo:** Alineado con cronograma
- **Frecuencia:** Semanal
- **Fórmula:** Presupuesto Gastado / Tiempo Transcurrido
- **Medición Actual:** $[VALOR_ACTUAL]/semana

---

## 🛠️ Métricas Técnicas

### Calidad de Desarrollo

#### Cobertura de Pruebas (%)
- **Definición:** Porcentaje del código cubierto por pruebas automatizadas
- **Objetivo:** ≥ 80%
- **Frecuencia:** Diaria (CI/CD)
- **Herramienta:** Jest/SonarQube
- **Medición Actual:** [VALOR_ACTUAL]%
- **Desglose:**
  - Frontend: [VALOR]%
  - Backend: [VALOR]%
  - APIs: [VALOR]%

#### Bugs por 1000 Líneas de Código
- **Definición:** Densidad de defectos en el código
- **Objetivo:** ≤ 5 bugs/1000 LOC
- **Frecuencia:** Semanal
- **Herramienta:** JIRA/Azure DevOps
- **Medición Actual:** [VALOR_ACTUAL] bugs/1000 LOC

#### Tiempo de Resolución de Bugs
- **Definición:** Tiempo promedio para resolver defectos
- **Objetivo:** 
  - Críticos: ≤ 4 horas
  - Altos: ≤ 24 horas
  - Medios: ≤ 72 horas
  - Bajos: ≤ 1 semana
- **Frecuencia:** Diaria
- **Medición Actual:** [VALORES_ACTUALES]

### Performance del Sistema

#### Tiempo de Respuesta de APIs (ms)
- **Definición:** Tiempo promedio de respuesta de endpoints críticos
- **Objetivo:** ≤ 500ms (percentil 95)
- **Frecuencia:** Continua
- **Herramienta:** New Relic/Datadog
- **Medición Actual:** [VALOR_ACTUAL]ms

#### Disponibilidad del Sistema (%)
- **Definición:** Porcentaje de tiempo que el sistema está operativo
- **Objetivo:** ≥ 99.5%
- **Frecuencia:** Continua
- **Fórmula:** (Tiempo Total - Tiempo de Caída) / Tiempo Total × 100
- **Medición Actual:** [VALOR_ACTUAL]%

#### Capacidad de Usuarios Concurrentes
- **Definición:** Número máximo de usuarios simultáneos soportados
- **Objetivo:** ≥ 500 usuarios concurrentes
- **Frecuencia:** Pruebas semanales de carga
- **Herramienta:** JMeter/LoadRunner
- **Medición Actual:** [VALOR_ACTUAL] usuarios

---

## 👥 Métricas de Equipo y Productividad

### Productividad del Equipo

#### Velocidad de Desarrollo (Story Points)
- **Definición:** Story points completados por sprint
- **Objetivo:** [VALOR_OBJETIVO] SP/sprint
- **Frecuencia:** Por sprint (2 semanas)
- **Herramienta:** JIRA/Azure DevOps
- **Tendencia:** [DESCRIPCIÓN_TENDENCIA]

#### Burndown Rate
- **Definición:** Velocidad de completado de tareas
- **Objetivo:** Línea ideal de burndown
- **Frecuencia:** Diaria
- **Medición:** Gráfico de burndown por sprint

#### Eficiencia del Equipo (%)
- **Definición:** Tiempo productivo vs tiempo total
- **Objetivo:** ≥ 75%
- **Frecuencia:** Semanal
- **Fórmula:** (Tiempo en Tareas Productivas / Tiempo Total) × 100
- **Medición Actual:** [VALOR_ACTUAL]%

### Satisfacción del Equipo

#### Índice de Satisfacción del Equipo (1-10)
- **Definición:** Encuesta de satisfacción del equipo de proyecto
- **Objetivo:** ≥ 7.5/10
- **Frecuencia:** Quincenal
- **Herramienta:** Encuesta anónima
- **Medición Actual:** [VALOR_ACTUAL]/10

#### Tasa de Rotación del Equipo (%)
- **Definición:** Porcentaje de miembros que dejan el proyecto
- **Objetivo:** ≤ 10% por año
- **Frecuencia:** Mensual
- **Fórmula:** (Personas que Salieron / Total del Equipo) × 100
- **Medición Actual:** [VALOR_ACTUAL]%

#### Horas Extra por Persona
- **Definición:** Promedio de horas extra trabajadas por semana
- **Objetivo:** ≤ 5 horas/semana por persona
- **Frecuencia:** Semanal
- **Herramienta:** Sistema de timetracking
- **Medición Actual:** [VALOR_ACTUAL] hrs/semana

---

## 📈 Métricas de Usuario y Adopción

### Adopción del Sistema

#### Tasa de Adopción por Departamento (%)
- **Definición:** Porcentaje de usuarios activos por departamento
- **Objetivo:** ≥ 85% en 30 días post-lanzamiento
- **Frecuencia:** Semanal
- **Desglose por Departamento:**
  - Ventas: [VALOR]%
  - Marketing: [VALOR]%
  - Administración: [VALOR]%
  - Soporte: [VALOR]%

#### Usuarios Activos Diarios (DAU)
- **Definición:** Número de usuarios únicos que usan el sistema diariamente
- **Objetivo:** [VALOR_OBJETIVO] usuarios/día
- **Frecuencia:** Diaria
- **Herramienta:** Google Analytics/Mixpanel
- **Tendencia:** [DESCRIPCIÓN_TENDENCIA]

#### Tiempo de Sesión Promedio
- **Definición:** Tiempo promedio que usuarios pasan en el sistema
- **Objetivo:** ≥ 45 minutos/sesión
- **Frecuencia:** Diaria
- **Medición Actual:** [VALOR_ACTUAL] minutos

### Satisfacción del Usuario

#### Net Promoter Score (NPS)
- **Definición:** Medida de satisfacción y lealtad del usuario
- **Objetivo:** ≥ 70
- **Frecuencia:** Mensual
- **Herramienta:** Encuesta NPS
- **Medición Actual:** [VALOR_ACTUAL]

#### Tasa de Tickets de Soporte por Usuario
- **Definición:** Número promedio de tickets por usuario mensual
- **Objetivo:** ≤ 0.5 tickets/usuario/mes
- **Frecuencia:** Mensual
- **Herramienta:** Sistema de ticketing
- **Medición Actual:** [VALOR_ACTUAL] tickets/usuario

#### Tiempo de Resolución de Consultas
- **Definición:** Tiempo promedio para resolver consultas de usuarios
- **Objetivo:** 
  - Nivel 1: ≤ 2 horas
  - Nivel 2: ≤ 24 horas
  - Nivel 3: ≤ 72 horas
- **Frecuencia:** Diaria
- **Medición Actual:** [VALORES_ACTUALES]

---

## 🔄 Métricas de Procesos

### Gestión de Cambios

#### Tiempo Promedio de Aprobación de Cambios
- **Definición:** Tiempo desde solicitud hasta aprobación de cambios
- **Objetivo:** ≤ 48 horas
- **Frecuencia:** Por cambio
- **Herramienta:** Sistema de workflow
- **Medición Actual:** [VALOR_ACTUAL] horas

#### Tasa de Cambios Rechazados (%)
- **Definición:** Porcentaje de solicitudes de cambio rechazadas
- **Objetivo:** ≤ 15%
- **Frecuencia:** Semanal
- **Fórmula:** (Cambios Rechazados / Total Solicitudes) × 100
- **Medición Actual:** [VALOR_ACTUAL]%

#### Impacto de Cambios en Cronograma
- **Definición:** Días de retraso promedio por cambio aprobado
- **Objetivo:** ≤ 2 días/cambio
- **Frecuencia:** Por cambio
- **Medición Actual:** [VALOR_ACTUAL] días

### Gestión de Riesgos

#### Número de Riesgos Activos
- **Definición:** Cantidad de riesgos identificados y no mitigados
- **Objetivo:** ≤ 5 riesgos altos
- **Frecuencia:** Semanal
- **Clasificación:**
  - Críticos: [NÚMERO]
  - Altos: [NÚMERO]
  - Medios: [NÚMERO]
  - Bajos: [NÚMERO]

#### Tiempo de Respuesta a Riesgos
- **Definición:** Tiempo desde identificación hasta implementación de mitigación
- **Objetivo:** ≤ 72 horas para riesgos altos
- **Frecuencia:** Por riesgo
- **Medición Actual:** [VALOR_ACTUAL] horas

---

## 📊 Dashboard de Métricas por Stakeholder

### Dashboard Ejecutivo (CEO/CTO)
**Métricas Clave:**
- Avance de fase: [VALOR]%
- Desviación presupuesto: [VALOR]%
- ROI parcial: [VALOR]%
- NPS: [VALOR]
- Riesgos críticos: [NÚMERO]

### Dashboard de Proyecto (PM)
**Métricas Clave:**
- Velocidad del equipo: [VALOR] SP/sprint
- Burndown rate: [ESTADO]
- Tiempo resolución bugs: [VALOR] horas
- Cambios pendientes: [NÚMERO]
- Satisfacción equipo: [VALOR]/10

### Dashboard Técnico (CTO/Tech Lead)
**Métricas Clave:**
- Cobertura pruebas: [VALOR]%
- Tiempo respuesta APIs: [VALOR]ms
- Disponibilidad: [VALOR]%
- Densidad bugs: [VALOR]/1000 LOC
- Performance: [ESTADO]

### Dashboard de Usuario (Business)
**Métricas Clave:**
- Adopción por departamento: [VALORES]%
- Usuarios activos: [VALOR]/día
- Tiempo sesión: [VALOR] min
- Tickets soporte: [VALOR]/usuario
- Capacitaciones completadas: [VALOR]%

---

## 🎯 Metas e Hitos por Fase

### Fase 1: Fundación (Enero 2026)
**Metas Principales:**
- [ ] Avance: 100%
- [ ] Presupuesto: ±3%
- [ ] Cobertura pruebas: ≥70%
- [ ] Satisfacción equipo: ≥7/10
- [ ] Riesgos críticos: 0

### Fase 2: Desarrollo Core (Febrero 2026)
**Metas Principales:**
- [ ] Avance: 100%
- [ ] Velocidad: 40 SP/sprint
- [ ] Bugs: ≤3/1000 LOC
- [ ] APIs: ≤400ms
- [ ] Disponibilidad: ≥99%

### Fase 3: Funcionalidades Avanzadas (Marzo 2026)
**Metas Principales:**
- [ ] Adopción piloto: ≥80%
- [ ] NPS piloto: ≥60
- [ ] Performance: ≤500ms
- [ ] Capacitaciones: 100%
- [ ] Documentación: 100%

### [Continuar para todas las fases...]

---

## 📈 Análisis de Tendencias

### Tendencias Positivas a Mantener
- **Velocidad del Equipo:** Incremento constante del 5% semanal
- **Satisfacción Usuario:** Mejora sostenida desde implementación
- **Calidad Código:** Reducción bugs del 20% mensual

### Tendencias Negativas a Corregir
- **Tiempo Respuesta:** Incremento del 10% última semana
- **Horas Extra:** Aumento del 15% último mes
- **Rotación Equipo:** 2 salidas no planificadas

### Acciones Correctivas Implementadas
1. **Optimización Base de Datos:** Para mejorar tiempo respuesta
2. **Refuerzo de Equipo:** 2 desarrolladores adicionales
3. **Plan de Retención:** Bonos y beneficios mejorados

---

## 🔄 Proceso de Reporte de Métricas

### Recolección de Datos

#### Fuentes Automatizadas
- **JIRA/Azure DevOps:** Story points, bugs, velocidad
- **CI/CD Pipeline:** Cobertura, builds, deployments
- **Monitoring Tools:** Performance, disponibilidad, errores
- **Analytics:** Usuarios, adopción, comportamiento

#### Fuentes Manuales
- **Encuestas:** Satisfacción equipo, NPS usuarios
- **Timetracking:** Horas trabajadas, horas extra
- **Reuniones:** Feedback stakeholders, impedimentos
- **Financiero:** Costos, presupuesto, ROI

### Frecuencia de Reporte

#### Diario (Automatizado)
- Métricas técnicas (performance, bugs, builds)
- Progreso de tareas
- Disponibilidad del sistema

#### Semanal (Semi-automatizado)
- Dashboard ejecutivo
- Reporte de progreso
- Métricas del equipo
- Análisis de tendencias

#### Mensual (Manual)
- Reporte ejecutivo completo
- Análisis profundo de métricas
- Recomendaciones estratégicas
- Actualización de objetivos

### Responsabilidades

#### Project Manager
- Consolidación de métricas generales
- Análisis de desviaciones
- Comunicación a stakeholders
- Plan de acciones correctivas

#### Tech Lead
- Métricas técnicas
- Análisis de calidad
- Performance del sistema
- Recomendaciones técnicas

#### SCRUM Master
- Métricas del equipo
- Productividad
- Impedimentos
- Mejora continua

---

## 🚨 Alertas y Escalaciones

### Alertas Automáticas

#### Críticas (Inmediata)
- Disponibilidad < 99%
- Tiempo respuesta APIs > 2000ms
- Bugs críticos en producción
- Desviación presupuesto > 15%

#### Altas (4 horas)
- Avance fase < 80% del objetivo semanal
- Satisfacción equipo < 6/10
- Velocidad equipo < 80% objetivo
- NPS usuarios < 50

#### Medias (24 horas)
- Cobertura pruebas < 70%
- Tiempo resolución bugs > objetivos
- Adopción usuarios < 70%
- Riesgos nuevos identificados

### Proceso de Escalación

#### Nivel 1: Project Manager
- Analiza la alerta
- Implementa acciones correctivas inmediatas
- Comunica a equipo afectado

#### Nivel 2: Steering Committee
- Revisa impacto en objetivos de fase
- Autoriza recursos adicionales
- Ajusta cronograma si necesario

#### Nivel 3: Dirección Ejecutiva
- Evalúa impacto en proyecto completo
- Decide continuidad o cambios mayores
- Comunica a todos los stakeholders

---

## 📋 Checklist de Implementación

### Configuración Inicial
- [ ] Definir objetivos específicos por métrica
- [ ] Configurar herramientas de medición
- [ ] Establecer baselines iniciales
- [ ] Crear dashboards automatizados
- [ ] Capacitar al equipo en métricas

### Ejecución Continua
- [ ] Recopilar datos diariamente
- [ ] Generar reportes semanales
- [ ] Analizar tendencias mensualmente
- [ ] Implementar acciones correctivas
- [ ] Actualizar objetivos según sea necesario

### Cierre de Fase
- [ ] Consolidar métricas finales de la fase
- [ ] Analizar cumplimiento de objetivos
- [ ] Documentar lecciones aprendidas
- [ ] Ajustar métricas para siguiente fase
- [ ] Comunicar resultados a stakeholders

---

## 📚 Herramientas y Recursos

### Herramientas de Medición
- **Project Management:** JIRA, Azure DevOps, Monday.com
- **Analytics:** Google Analytics, Mixpanel, Hotjar
- **Monitoring:** New Relic, Datadog, Pingdom
- **Reporting:** Power BI, Tableau, Google Data Studio
- **Surveys:** SurveyMonkey, Typeform, Microsoft Forms

### Templates de Reportes
- Dashboard ejecutivo semanal
- Reporte técnico mensual
- Análisis de tendencias trimestral
- Alerta de desviación crítica
- Evaluación de fase completa

---

## ✅ Validación y Aprobación

### Responsable de Métricas
**Nombre:** [NOMBRE_RESPONSABLE]
**Firma:** ________________
**Fecha:** [DD/MM/AAAA]

### Aprobación Project Manager
**Nombre:** [NOMBRE_PM]
**Firma:** ________________
**Fecha:** [DD/MM/AAAA]

### Aprobación Dirección
**Nombre:** [NOMBRE_DIRECTOR]
**Cargo:** [CARGO]
**Firma:** ________________
**Fecha:** [DD/MM/AAAA]

### Notas de Implementación
[ESPACIO_PARA_OBSERVACIONES_ESPECÍFICAS_DE_LA_FASE]

---

*Template creado para el Proyecto InmoTech - Sistema de Gestión Inmobiliaria*
*Versión 1.0 | Noviembre 2025 | Equipo de Proyecto*