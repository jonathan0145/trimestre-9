# Reporte/Artefacto de Análisis Estático de Código

## Objetivo
Documentar los resultados del análisis estático realizado sobre el código fuente del proyecto, identificando vulnerabilidades, "code smells", errores y recomendaciones de mejora.

## Herramienta utilizada
- Ejemplo: SonarQube (puedes especificar otra si usas diferente)

## Alcance del análisis
- Módulos, carpetas o archivos analizados
- Fecha de ejecución
- Versión del código analizado

## Resultados principales
- Número total de issues encontrados
- Clasificación de issues (bloqueantes, críticos, mayores, menores, informativos)
- Principales vulnerabilidades detectadas
- "Code smells" más frecuentes
- Errores de seguridad
- Duplicidad de código
- Cobertura de pruebas (si la herramienta lo reporta)

## Ejemplo de tabla de resultados
| Tipo de Issue      | Cantidad | Ejemplos/Ubicación |
|--------------------|----------|--------------------|
| Vulnerabilidades   |    3     | src/controllers/user.js |
| Code Smells        |   15     | src/services/auth.js |
| Bugs               |    2     | src/models/profile.js |
| Duplicidad de código |  1     | src/utils/helpers.js |

## Recomendaciones de mejora
- Acciones sugeridas para resolver los issues críticos
- Refactorizaciones propuestas
- Mejoras de seguridad
- Acciones para aumentar la cobertura de pruebas

## Evidencia

## Seguimiento


> Este documento debe actualizarse tras cada ejecución relevante del análisis estático y servir como referencia para auditorías y mejora continua.

---

## Resumen de análisis SonarQube

### Backend (inmotech)
- **Quality Gate:** Passed
- **Líneas de código:** 4.6k
- **Seguridad:** 0 issues abiertos
- **Fiabilidad:** 6 issues abiertos
- **Mantenibilidad:** 57 issues abiertos
- **Cobertura de pruebas:** 0.0% (1.7k líneas por cubrir)
- **Duplicidad:** 3.4% (en 6.3k líneas)
- **Security Hotspots:** 18

**Recomendaciones backend:**
- Revisar los 6 issues de fiabilidad y los 57 de mantenibilidad para mejorar la calidad del código.
- Incrementar la cobertura de pruebas unitarias.
- Reducir la duplicidad de código.
- Analizar y mitigar los 18 security hotspots.

#### Evidencias PDF Backend
- [PDF 1](./sonnar3.pdf)
- [PDF 2](./sonnar5.pdf)
- [PDF 3](./sonnar6.pdf)

---

### Frontend (Inmotech Frontend)
- **Quality Gate:** Aprobado
- **Líneas de código:** 16k
- **Seguridad:** 0 cuestiones abiertas
- **Fiabilidad:** 190 cuestiones abiertas
- **Mantenibilidad:** 391 cuestiones abiertas
- **Cobertura de pruebas:** 0.0% (2.9k líneas por cubrir)
- **Duplicidad:** 3.8% (en 19k líneas)
- **Puntos críticos de seguridad:** 2

**Recomendaciones frontend:**
- Revisar los 190 issues de fiabilidad y los 391 de mantenibilidad para mejorar la calidad del código.
- Incrementar la cobertura de pruebas unitarias.
- Reducir la duplicidad de código.
- Analizar y mitigar los 2 puntos críticos de seguridad.

---

#### Evidencias PDF Frontend
- [PDF 4](./sonnar4.pdf)
- [PDF 5](./sonnar5.pdf)
- [PDF 6](./sonnar6.pdf)