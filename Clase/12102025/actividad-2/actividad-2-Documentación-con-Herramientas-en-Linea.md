# Actividad 2: Documentación con Herramientas en Línea
Servicio Nacional de Aprendizaje

Objetivo:  Crear documentación de calidad usando herramientas colaborativas

Descripción:
● Crear un repositorio en GitHub/GitLab: [https://github.com/jonathan0145/trimestre-9.git](https://github.com/jonathan0145/trimestre-9.git)

● Documentar un proceso de testing usando wiki o pages: [https://docs.google.com/document/d/1xP5G1V26OaEZoZ9Rlh7YPcHlqTSuV8TwBTs5sGrHyVQ/edit?usp=sharing](https://docs.google.com/document/d/1xP5G1V26OaEZoZ9Rlh7YPcHlqTSuV8TwBTs5sGrHyVQ/edit?usp=sharing)

● Crear diagramas de flujo del proceso con Draw.io: [Ver diagrama](./diagrama-flujo-proceso-pruebas/Diagrama-de-flujo-de-proceso.md)
● Establecer un board de Trello para seguimiento

Artefactos a entregar:
● Repositorio con documentación
● Diagramas del proceso
● Board de Trello con tareas

## Diccionario de términos técnicos

- Crear repositorio: acción de inicializar o alojar un repositorio Git en una plataforma (GitHub/GitLab). Incluye crear la estructura inicial (README.md, .gitignore) y las ramas necesarias; es el punto central donde se almacenan y versionan todos los cambios del proyecto.

- README.md: archivo de documentación principal del proyecto que resume su propósito, requisitos, instalación, comandos esenciales y enlaces. Sirve como guía rápida para cualquier colaborador y suele contener badges que indican el estado del CI o la cobertura.

- .gitignore: fichero que lista archivos y carpetas que Git debe ignorar (por ejemplo dependencias locales, archivos de configuración personal o binarios). Evita que información innecesaria o sensible se suba al repositorio y mantiene el historial limpio.

- Rama / branch: línea paralela de desarrollo en Git que permite trabajar aislado sin afectar la rama principal (main). Se usan para features, fixes o experimentos y facilitan revisiones y merges controlados.

- Configurar CI (Integración Continua): preparar el sistema automático que ejecuta tareas al detectar cambios en el repo (builds, tests, checks). Incluye definir workflows y jobs que instalen dependencias, ejecuten pruebas y generen artefactos; su objetivo es detectar fallos lo antes posible.

- Plantilla de issues: formato predefinido para crear issues de manera consistente (campos como título, descripción, pasos para reproducir, resultado esperado, entorno). Facilita que quien recibe el reporte tenga toda la información necesaria para reproducir y resolver el problema.

- Caso de prueba: especificación que describe un escenario a comprobar, incluyendo objetivo, precondiciones, pasos, datos de prueba y resultado esperado. Permite validar una funcionalidad concreta de forma repetible y documentada.

- Precondiciones: condiciones o estado inicial que deben cumplirse antes de ejecutar un caso de prueba (ej. datos existentes, configuración de entorno). Aseguran que la prueba se ejecute en el contexto correcto y sea reproducible.

- Pasos (del caso de prueba) / Pasos para reproducir: secuencia numerada de acciones que se deben realizar para ejecutar la prueba o recrear un bug. Deben ser claras y precisas para que cualquier persona pueda seguirlas y obtener el mismo resultado.

- Datos de prueba: valores de entrada (ej. usuarios, campos, configuraciones) utilizados durante la ejecución de un caso de prueba. Especificarlos permite reproducir exactamente el escenario y analizar fallos relacionados con datos.

- Resultado esperado: descripción del comportamiento correcto o salida que debe producir el sistema cuando la prueba pasa. Sirve como criterio objetivo para decidir si la prueba ha sido satisfactoria.

- Implementar tests / Tests (pruebas): escribir los scripts o archivos que automatizan los casos de prueba. Incluye seguir la convención de nombres y ubicación (por ejemplo la carpeta tests/), y usar un framework apropiado para el lenguaje (pytest, jest, etc.) para ejecutar y reportar resultados.

- Convención (de tests): normas de nombres y estructura de carpetas para los tests que facilitan su descubrimiento automático y mantenimiento. Ejemplos: prefijos/sufijos para archivos de test, estructuras por módulo o por tipo de prueba.

- Ejecutar tests (local / CI): acción de correr la suite de pruebas en un entorno de desarrollo local o en la pipeline de CI. Requiere documentar el comando exacto a usar, la versión del entorno y cualquier variable necesaria para obtener resultados reproducibles.

- Comando: instrucción de terminal que se ejecuta para correr builds o tests (ej. pytest -q, npm test). Debe especificarse exactamente para evitar diferencias entre entornos.

- Logs: registros textuales generados durante la ejecución de tests o procesos que documentan pasos, errores y salidas. Son la primera fuente de información para diagnosticar fallos y deben adjuntarse como evidencia cuando se crea un issue.

- ¿Tests OK? (decisión): paso en el flujo que determina si la ejecución de tests fue satisfactoria. Si la respuesta es No se abre el camino de reporte y corrección; si es Sí se continúa con generación de reportes y documentación.

- Crear issue / Reportar bug: registrar un problema en el gestor de issues del repo indicando título, descripción, pasos para reproducir, resultado observado y evidencias. Debe incluir quién está asignado y el entorno para acelerar su resolución.

- Bug: defecto o comportamiento incorrecto del software detectado mediante pruebas o uso real. Debe documentarse con evidencias y pasos para reproducir antes de ser corregido.

- Evidencias: archivos o datos que demuestran el fallo (logs, capturas de pantalla, archivos de reporte). Permiten al desarrollador reproducir y confirmar el problema sin depender únicamente de la descripción textual.

- Asignado: usuario responsable al que se le ha asignado el issue para su seguimiento y resolución. Indicar responsable agiliza la gestión y evita duplicidades.

- Corregir y commitear / Commit: realizar el cambio en el código para corregir el bug y registrar esos cambios en Git con un commit que incluya un mensaje descriptivo. El commit forma parte de la historia y debe referenciar el issue cuando aplique.

- Rama feature/fix: rama específica creada para desarrollar una funcionalidad nueva (feature) o una corrección (fix). Mantener cambios en estas ramas facilita revisiones y pruebas aisladas antes de integrarlos a la rama principal.

- Referencia al issue (ref issue): práctica de incluir en el mensaje de commit o en el Pull Request el identificador del issue (por ejemplo #12) para enlazar automáticamente la corrección con el reporte original y mantener trazabilidad.

- Generar reporte de pruebas (HTML / JSON / XML): producir archivos que resumen los resultados de la ejecución de tests, incluyendo pasadas/fallidas, tiempos y detalles de errores. Estos reportes son útiles para inspección humana y para integrarlos en otras herramientas (dashboards, cobertura).

- Métricas (de testing): valores cuantitativos extraídos de los tests y reportes (número de tests, fallos, tiempos, tasa de éxito). Ayudan a evaluar la estabilidad y salud del proyecto a lo largo del tiempo.

- Cobertura (coverage): medida del porcentaje de código ejecutado por las pruebas automatizadas. Indica qué partes del código están probadas, aunque no garantiza que todas las funcionalidades estén verificadas correctamente.

- Documentar en Wiki / Pages: publicar guías, procedimientos y resultados en espacios de documentación asociados al repositorio (GitHub Wiki o Pages). Debe incluir instrucciones reproducibles, enlaces a reportes y artefactos relevantes.

- Actualizar Trello / Trello board: mover tarjetas en un tablero visual para reflejar el estado de las tareas (por ejemplo mover a "Done") y adjuntar evidencias. El tablero sirve para seguimiento visual y coordinación del equipo.

- Tarjeta / card: unidad de trabajo en Trello que describe una tarea, su descripción, responsables y adjuntos. Es el elemento que se actualiza durante el flujo de trabajo.

- Done: estado final que indica que la tarea o tarjeta ha sido completada y las evidencias correspondientes fueron adjuntadas.

- Artefactos: ficheros generados y relevantes del proceso de testing que se deben conservar (ejemplos específicos mostrados en la imagen: badge CI, tests/report.html, screenshots/, links a issue/PR, archivo .drawio). Son la evidencia tangible de lo realizado y deben estar accesibles desde la documentación.

- Badge: imagen pequeña (normalmente SVG o PNG) que se coloca en el README u otra documentación para mostrar de un vistazo el estado de una métrica o workflow (por ejemplo build passing/failing, tests, coverage). El badge se genera desde la plataforma de CI o servicio que publica el resultado y contiene un enlace a la ejecución o al servicio; su propósito es ofrecer una señal visual inmediata sobre la salud del proyecto y promover que los colaboradores revisen fallos visibles.

- Framework (de pruebas): conjunto estructurado de librerías, utilidades y convenciones que facilita escribir, organizar y ejecutar pruebas automatizadas. Un framework provee aserciones, gestión de fixtures, discovery de tests, runner y formatos de reporte (ej.: pytest para Python, jest para JavaScript, JUnit para Java). Elegir un framework determina la convención de nombres, la estructura de carpetas, los comandos de ejecución y la forma en que se integran los reportes/cobertura en CI.
