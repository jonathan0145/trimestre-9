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
- Capturas de pantalla del reporte generado por la herramienta
- Enlace al reporte completo (si está disponible en línea)

## Seguimiento
- Responsable de la corrección
- Fecha estimada de resolución
- Estado de avance

---

> Este documento debe actualizarse tras cada ejecución relevante del análisis estático y servir como referencia para auditorías y mejora continua.