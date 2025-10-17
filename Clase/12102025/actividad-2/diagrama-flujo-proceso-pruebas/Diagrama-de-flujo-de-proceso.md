# Diagrama de flujo del proceso

Este documento está destinado a incluir el diagrama de flujo del proceso realizado con Draw.io para la Actividad 2.

Puedes insertar aquí una imagen exportada del diagrama, un enlace al archivo en línea, o una breve descripción del flujo del proceso documentado.

**Enlace al diagrama:**
[Diagrama en Draw.io](https://drive.google.com/file/d/1NQb3mFCf4qPLfUIWpXHXIwlWuklpzunb/view?usp=sharing)

**Descripción breve:**
- Inicio
- Preparación del entorno
- Ejecución de pruebas
- Documentación de resultados
- Seguimiento en Trello
- Fin

## Desarrollo

Este diagrama muestra el proceso de documentación de un proceso de testing. Sugerencias para crear el diagrama en Draw.io: figuras, texto que contienen y conectores.

1) Estructura general
- Usar swimlanes (opcional): Developer / CI / Reviewer / Project Management (Trello).
- Colores: procesos azules, decisiones grises, éxito verde, fallo rojo, artefactos amarillos.
- Exportar como PNG/SVG y adjuntar en el repositorio. Nombre sugerido: actividad-2-diagrama-flujo-testing.png

2) Figuras y contenido (orden secuencial)
- Óvalo (Start)
  - Texto: Inicio
- Rectángulo (Proceso)
  - Texto: Crear repositorio (GitHub/GitLab)
  - Contenido: README.md, licencia, .gitignore, rama main/dev
- Rectángulo (Proceso)
  - Texto: Configurar CI y plantilla de issues
  - Contenido: workflow CI, badge en README
- Rectángulo (Proceso)
  - Texto: Diseñar casos de prueba
  - Contenido: objetivo, precondiciones, pasos, datos de prueba, resultado esperado
- Rectángulo (Proceso)
  - Texto: Implementar tests
  - Contenido: ubicación de archivos (tests/), convención de nombres, frameworks y comandos (ej. pytest, npm test)
- Rectángulo (Proceso)
  - Texto: Ejecutar tests (local / CI)
  - Contenido: comando exacto, versión de entorno, logs
- Diamante (Decisión)
  - Texto: ¿Tests OK?
  - Conectores: "Sí" (hacia la derecha), "No" (hacia abajo)
- Si "No" (flujo de fallo)
  - Rectángulo: Crear issue / Reportar bug
    - Contenido: pasos para reproducir, logs, evidencias, asignado
  - Rectángulo: Corregir y commitear
    - Contenido: rama feature/fix, referencia al issue
  - Flecha: volver a "Implementar tests"
- Si "Sí" (flujo de éxito)
  - Rectángulo: Generar reporte de pruebas
    - Contenido: HTML/JSON/XML, resumen de métricas, cobertura
  - Documento (shape "Document")
    - Texto: Documentar en Wiki / Pages
    - Contenido: enlace al repo, pasos reproducibles, artefactos (reportes, capturas)
  - Rectángulo: Actualizar Trello (board)
    - Contenido: mover tarjetas, adjuntar evidencias, estado: Done
- Óvalo (End)
  - Texto: Fin

3) Conectores y anotaciones
- Etiquetar flechas de decisión: "Sí" / "No".
- Añadir notas en el diagrama con:
  - Comandos exactos para ejecutar (ej. "pytest -q --maxfail=1", "npm test").
  - Ruta de archivos de tests y reports.
  - Enlaces: repo URL, wiki URL, Trello board URL.

4) Artefactos a incluir como nodos/documentos
- Badge CI en README
- Reporte de pruebas (tests/report.html)
- Capturas de fallos (screenshots/)
- Issue link y PR link
- Diagrama exportado y el archivo .drawio adjunto

5) Alt text y exportación
- Alt text sugerido: "Diagrama de flujo del proceso de testing: crear repo → diseñar pruebas → ejecutar → decisión tests ok → documentar o reportar bug → actualizar Trello".
- Exportar PNG y SVG; subir .drawio al repo para edición futura.

## Diagrama de flujo

![Diagrama de flujo del proceso de testing](./actividad-2-diagrama-flujo-testing.png "Diagrama de flujo del proceso de testing")

Descargar archivo editable: [actividad-2-diagrama-flujo-testing.drawio](./actividad-2-diagrama-flujo-testing.drawio)

