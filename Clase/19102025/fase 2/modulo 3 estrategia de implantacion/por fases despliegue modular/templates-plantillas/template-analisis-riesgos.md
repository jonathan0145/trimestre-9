# Plantilla - Análisis de Riesgos por Fase

## Información de la Fase

**Nombre de la Fase:** [NOMBRE_FASE]
**Número de Fase:** [NUMERO]
**Fecha de Análisis:** [FECHA]
**Responsable del Análisis:** [RESPONSABLE_RIESGOS]
**Revisor/Aprobador:** [REVISOR]

---

## 🎯 Resumen Ejecutivo de Riesgos

### Nivel de Riesgo General de la Fase
- [ ] **🟢 Bajo** - Riesgos controlados, probabilidad/impacto mínimos
- [ ] **🟡 Medio** - Riesgos moderados, requiere monitoreo
- [ ] **🔴 Alto** - Riesgos significativos, requiere planes de contingencia
- [ ] **⚫ Crítico** - Riesgos que pueden comprometer el proyecto

### Riesgos Más Críticos (Top 3)
1. **[RIESGO_1]** - Probabilidad: [X]%, Impacto: [Y]
2. **[RIESGO_2]** - Probabilidad: [X]%, Impacto: [Y]
3. **[RIESGO_3]** - Probabilidad: [X]%, Impacto: [Y]

---

## 📋 Matriz de Riesgos Detallada

### 🔧 Riesgos Técnicos

#### RT01: [Descripción del Riesgo Técnico]
| Campo | Valor |
|-------|-------|
| **Descripción** | [Descripción detallada del riesgo] |
| **Categoría** | Backend/Frontend/Infraestructura/Integración |
| **Probabilidad** | 🟢 Baja (1-25%) / 🟡 Media (26-50%) / 🔴 Alta (51-75%) / ⚫ Muy Alta (76-100%) |
| **Impacto** | 🟢 Mínimo / 🟡 Menor / 🔴 Moderado / ⚫ Severo / 🔥 Catastrófico |
| **Nivel de Riesgo** | [Probabilidad × Impacto] |
| **Triggers/Indicadores** | • [Indicador 1]<br>• [Indicador 2]<br>• [Indicador 3] |
| **Síntomas de Activación** | • [Síntoma 1]<br>• [Síntoma 2] |

**Plan de Mitigación:**
- [ ] **Prevención:** [Acciones preventivas]
- [ ] **Monitoreo:** [Métricas a vigilar]
- [ ] **Respuesta:** [Acciones correctivas]

**Plan de Contingencia (si se materializa):**
1. [Paso 1 de contingencia]
2. [Paso 2 de contingencia]
3. [Paso 3 de contingencia]

**Responsable:** [Nombre]
**Fecha Límite de Revisión:** [Fecha]

---

#### RT02: [Segundo Riesgo Técnico]
| Campo | Valor |
|-------|-------|
| **Descripción** | [Descripción detallada] |
| **Categoría** | [Categoría] |
| **Probabilidad** | [Nivel] |
| **Impacto** | [Nivel] |
| **Nivel de Riesgo** | [Cálculo] |

[Repetir estructura para cada riesgo técnico]

---

### 👥 Riesgos de Recursos Humanos

#### RH01: [Disponibilidad de Personal Clave]
| Campo | Valor |
|-------|-------|
| **Descripción** | Falta de disponibilidad del personal especializado |
| **Probabilidad** | [Nivel] |
| **Impacto** | [Nivel] |
| **Recursos Críticos** | • [Desarrollador Backend]<br>• [Desarrollador Frontend]<br>• [DBA/DevOps] |
| **Plan de Contingencia** | • Recursos de backup identificados<br>• Cross-training implementado<br>• Consultores externos en standby |

#### RH02: [Curva de Aprendizaje]
| Campo | Valor |
|-------|-------|
| **Descripción** | Personal requiere tiempo adicional para dominar nuevas tecnologías |
| **Probabilidad** | [Nivel] |
| **Impacto** | [Nivel] |
| **Tecnologías de Riesgo** | • [Tecnología 1]<br>• [Tecnología 2] |
| **Plan de Mitigación** | • Capacitación previa<br>• Documentación técnica<br>• Mentoring |

---

### 🔗 Riesgos de Integración

#### RI01: [Compatibilidad con Fases Anteriores]
| Campo | Valor |
|-------|-------|
| **Descripción** | La nueva funcionalidad puede afectar módulos ya implementados |
| **Dependencias Críticas** | • [Módulo/Fase 1]<br>• [Módulo/Fase 2] |
| **Puntos de Integración** | • [API endpoints]<br>• [Base de datos]<br>• [Estados compartidos] |
| **Plan de Validación** | • Smoke tests<br>• Regression testing<br>• Integration testing |

#### RI02: [Dependencias Externas]
| Campo | Valor |
|-------|-------|
| **Descripción** | Servicios externos o APIs de terceros pueden fallar |
| **Servicios Críticos** | • [Servicio 1]<br>• [API Externa]<br>• [Proveedor Cloud] |
| **Plan de Contingencia** | • Servicios de backup<br>• Mock services<br>• Circuit breakers |

---

### 📅 Riesgos de Cronograma

#### RC01: [Retrasos en Dependencias]
| Campo | Valor |
|-------|-------|
| **Descripción** | Fases anteriores pueden retrasarse afectando el cronograma |
| **Dependencias Críticas** | • [Fase X debe completarse]<br>• [Entregable Y requerido] |
| **Buffer de Tiempo** | [X] días de holgura incluidos |
| **Plan de Recuperación** | • Trabajo paralelo donde sea posible<br>• Priorización de tareas críticas<br>• Recursos adicionales si es necesario |

#### RC02: [Complejidad Subestimada]
| Campo | Valor |
|-------|-------|
| **Descripción** | La complejidad técnica puede ser mayor a la estimada |
| **Áreas de Incertidumbre** | • [Funcionalidad compleja 1]<br>• [Integración compleja 2] |
| **Factores de Riesgo** | • Primera implementación de [tecnología]<br>• Requisitos poco claros en [área] |

---

### 🔐 Riesgos de Seguridad

#### RS01: [Vulnerabilidades de Seguridad]
| Campo | Valor |
|-------|-------|
| **Descripción** | Nuevas funcionalidades pueden introducir vulnerabilidades |
| **Vectores de Ataque** | • [Vector 1]<br>• [Vector 2] |
| **Datos Sensibles** | • [Tipo de datos 1]<br>• [Información crítica 2] |
| **Plan de Mitigación** | • Security testing<br>• Code review de seguridad<br>• Penetration testing |

#### RS02: [Cumplimiento y Regulaciones]
| Campo | Valor |
|-------|-------|
| **Descripción** | Riesgo de no cumplir con regulaciones aplicables |
| **Regulaciones Aplicables** | • [GDPR/CCPA]<br>• [Regulaciones locales]<br>• [Standards industria] |
| **Plan de Cumplimiento** | • Audit de compliance<br>• Documentación requerida<br>• Certificaciones necesarias |

---

## 📊 Dashboard de Monitoreo de Riesgos

### Métricas Clave a Monitorear

| Métrica | Threshold Verde | Threshold Amarillo | Threshold Rojo | Frecuencia de Revisión |
|---------|----------------|-------------------|----------------|----------------------|
| **Tiempo de Desarrollo** | < 90% estimado | 90-110% estimado | > 110% estimado | Diario |
| **Bugs Críticos** | 0-2 | 3-5 | > 5 | Diario |
| **Performance API** | < 200ms | 200-500ms | > 500ms | Tiempo real |
| **Cobertura de Tests** | > 80% | 70-80% | < 70% | Por commit |
| **Disponibilidad del Equipo** | 100% | 80-99% | < 80% | Semanal |

### Triggers de Escalamiento

#### 🟡 Escalamiento Nivel 1 - Líder Técnico
- Cualquier métrica en threshold amarillo por > 2 días
- Aparición de 2+ riesgos medios simultáneos
- Retraso > 1 día en entregables críticos

#### 🔴 Escalamiento Nivel 2 - Project Manager
- Cualquier métrica en threshold rojo
- Materialización de riesgo alto
- Retraso > 3 días en cronograma
- Indisponibilidad de recurso crítico > 24h

#### ⚫ Escalamiento Nivel 3 - Dirección
- Múltiples métricas en rojo simultáneamente
- Riesgo crítico materializado
- Posible compromiso de fecha de entrega de fase
- Problemas de seguridad severos

---

## 🔄 Plan de Seguimiento y Revisión

### Cronograma de Revisiones

| Tipo de Revisión | Frecuencia | Participantes | Duración |
|------------------|------------|---------------|----------|
| **Revisión Diaria** | Diaria | Equipo técnico | 15 min |
| **Revisión Semanal** | Semanal | Líder técnico + PM | 30 min |
| **Revisión de Fase** | Al finalizar fase | Todos los stakeholders | 60 min |
| **Revisión de Crisis** | Según necesidad | Equipo directivo | Variable |

### Documentación de Cambios

| Fecha | Riesgo Modificado | Cambio Realizado | Justificación | Responsable |
|-------|-------------------|------------------|---------------|-------------|
| [Fecha] | [ID Riesgo] | [Descripción cambio] | [Razón] | [Nombre] |

---

## 📋 Checklist de Cierre de Análisis

### Pre-Inicio de Fase
- [ ] Todos los riesgos identificados y documentados
- [ ] Planes de mitigación definidos y asignados
- [ ] Recursos de contingencia identificados
- [ ] Métricas de monitoreo configuradas
- [ ] Triggers de escalamiento comunicados al equipo
- [ ] Revisiones programadas en calendario
- [ ] Aprobación del análisis por PM y líder técnico

### Durante la Fase
- [ ] Monitoreo diario de métricas activo
- [ ] Revisiones semanales ejecutándose
- [ ] Registro de incidencias actualizado
- [ ] Planes de contingencia activados cuando necesario
- [ ] Comunicación de riesgos a stakeholders

### Post-Fase
- [ ] Lecciones aprendidas documentadas
- [ ] Riesgos materializados analizados
- [ ] Efectividad de mitigaciones evaluada
- [ ] Recomendaciones para fases futuras
- [ ] Actualización de templates basada en experiencia

---

## 📞 Contactos de Emergencia

### Equipo de Gestión de Riesgos

| Rol | Nombre | Teléfono | Email | Disponibilidad |
|-----|--------|----------|-------|----------------|
| **Risk Manager** | [Nombre] | [Teléfono] | [Email] | 24/7 |
| **Tech Lead** | [Nombre] | [Teléfono] | [Email] | 8AM-10PM |
| **Project Manager** | [Nombre] | [Teléfono] | [Email] | 7AM-9PM |
| **DevOps Lead** | [Nombre] | [Teléfono] | [Email] | 24/7 on-call |

### Proveedores Críticos

| Servicio | Proveedor | Contacto Soporte | SLA | Escalación |
|----------|-----------|------------------|-----|------------|
| **Hosting Cloud** | [Proveedor] | [Contacto] | [SLA] | [Proceso] |
| **Base de Datos** | [Proveedor] | [Contacto] | [SLA] | [Proceso] |
| **APIs Externas** | [Proveedor] | [Contacto] | [SLA] | [Proceso] |

---

**📝 Notas de Uso:**
1. Adaptar esta plantilla para cada fase específica
2. Actualizar riesgos basado en lecciones aprendidas de fases anteriores
3. Mantener comunicación proactiva con stakeholders sobre riesgos altos
4. Revisar y actualizar planes de contingencia regularmente

**🔄 Última Actualización:** [Fecha]
**📌 Versión:** 1.0
**✅ Estado:** [Borrador/En Revisión/Aprobado]