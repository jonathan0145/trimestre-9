# FORMATO DE REGISTRO DE INCIDENCIAS - PILOTO INMOTECH

**Proyecto:** Inmotech  
**Fase:** Piloto - Implementación en Área Pequeña  
**Versión:** 1.0  
**Fecha:** 21 de octubre de 2025  

---

## 📋 INSTRUCCIONES DE USO

Este formato debe ser usado por todos los usuarios piloto para registrar:
- ❌ **Errores o fallos del sistema**
- ⚠️ **Dificultades en el uso**
- 💡 **Sugerencias de mejora**
- ✅ **Aspectos positivos destacables**

**¡Importante!** Completa un registro por cada incidencia individual para facilitar el seguimiento.

---

## 📝 PLANTILLA DE REGISTRO

### **INFORMACIÓN BÁSICA**

| Campo | Valor |
|-------|-------|
| **ID de Incidencia** | [Automático: INC-YYYY-MM-DD-XXX] |
| **Fecha y Hora** | [DD/MM/YYYY - HH:MM] |
| **Usuario Reporta** | [Nombre - Perfil] |
| **Módulo Afectado** | [Seleccionar: Login, Dashboard, Propiedades, Ofertas, Chat, etc.] |
| **Tipo de Incidencia** | [Seleccionar: Error, Dificultad, Sugerencia, Aspecto Positivo] |
| **Prioridad** | [Seleccionar: Alta, Media, Baja] |

### **DESCRIPCIÓN DETALLADA**

#### **¿Qué pasó?** (Descripción clara y concisa)
```
[Describe en 1-2 párrafos qué ocurrió, qué esperabas que pasara vs. qué realmente pasó]

Ejemplo: "Al intentar subir fotos de una propiedad, solo pude cargar 3 de las 8 fotos seleccionadas. Esperaba que se cargaran todas las fotos simultáneamente."
```

#### **¿Cómo pasó?** (Pasos para reproducir)
```
1. [Primer paso específico]
2. [Segundo paso específico]  
3. [Tercer paso específico]
...

Ejemplo:
1. Accedí al módulo "Gestión de Propiedades"
2. Hice clic en "Nueva Propiedad"
3. Completé información básica (título, precio, ubicación)
4. En la sección "Fotos", hice clic en "Subir múltiples"
5. Seleccioné 8 archivos JPG (cada uno menor a 2MB)
6. Hice clic en "Cargar fotos"
7. Solo se cargaron las primeras 3 fotos
```

#### **¿Cuándo pasó?** (Contexto temporal)
```
[Indica si es la primera vez, si pasa siempre, solo a veces, etc.]

Ejemplo: "Pasa cada vez que intento subir más de 5 fotos simultáneamente. Con 3-4 fotos funciona bien."
```

### **IMPACTO Y CLASIFICACIÓN**

| Aspecto | Evaluación |
|---------|------------|
| **Impacto en tu trabajo** | [Alto/Medio/Bajo] |
| **Frecuencia del problema** | [Siempre/Frecuente/Ocasional/Una vez] |
| **¿Bloquea tu trabajo?** | [Sí/No] |
| **¿Hay solución temporal?** | [Sí/No - describir si aplica] |

### **EVIDENCIA ADJUNTA**

- [ ] **Captura de pantalla** (archivo adjunto)
- [ ] **Video del problema** (archivo adjunto o enlace)
- [ ] **Logs de consola** (si aplica)
- [ ] **Mensaje de error exacto** (texto copiado)

**Archivos adjuntos:**
```
[Lista aquí los nombres de archivos que adjuntas como evidencia]

Ejemplo:
- error_carga_fotos_screenshot.png
- video_proceso_fallo.mp4
```

### **SUGERENCIA DE SOLUCIÓN** (Opcional)

```
[Si tienes ideas de cómo podría solucionarse o mejorarse, compártelas aquí]

Ejemplo: "Podría mostrar una barra de progreso para cada foto siendo cargada, y un mensaje de error específico si alguna falla."
```

---

## 📊 EJEMPLOS DE REGISTRO

### **EJEMPLO 1: ERROR TÉCNICO**

| Campo | Valor |
|-------|-------|
| **ID de Incidencia** | INC-2025-10-23-001 |
| **Fecha y Hora** | 23/10/2025 - 10:30 |
| **Usuario Reporta** | Ana Torres - Comprador |
| **Módulo Afectado** | Búsqueda de Propiedades |
| **Tipo de Incidencia** | Error |
| **Prioridad** | Alta |

**¿Qué pasó?**
Al aplicar filtros de búsqueda (precio entre $200K-300K + 2 habitaciones), la página se queda en blanco y no muestra resultados. Esperaba ver una lista de propiedades que cumplieran los criterios.

**¿Cómo pasó?**
1. Accedí a "Búsqueda de Propiedades"
2. Establecí precio mínimo: $200,000
3. Establecí precio máximo: $300,000
4. Seleccioné "2 habitaciones" en filtro de habitaciones
5. Hice clic en "Buscar"
6. La página se queda en blanco con un ícono de carga infinito

**¿Cuándo pasó?**
Pasa siempre que uso esa combinación específica de filtros. Con otros rangos de precio funciona bien.

### **EJEMPLO 2: SUGERENCIA DE MEJORA**

| Campo | Valor |
|-------|-------|
| **ID de Incidencia** | INC-2025-10-23-002 |
| **Fecha y Hora** | 23/10/2025 - 14:15 |
| **Usuario Reporta** | Luis Gómez - Vendedor |
| **Módulo Afectado** | Dashboard de Vendedor |
| **Tipo de Incidencia** | Sugerencia |
| **Prioridad** | Media |

**¿Qué pasó?**
El dashboard muestra bien las métricas, pero sería muy útil poder filtrar las estadísticas por período personalizado (ej: últimos 15 días, último mes, último trimestre).

**Sugerencia de Solución:**
Agregar un selector de fechas en la parte superior del dashboard que permita elegir rangos personalizados, similar a Google Analytics.

---

## 🚨 PROCEDIMIENTO DE ESCALAMIENTO

### **Incidencias CRÍTICAS (Prioridad Alta):**
1. **Registrar inmediatamente** usando este formato
2. **Notificar por WhatsApp** a Carlos Mendoza (Ext. 305)
3. **Copiar a** María González (Ext. 201) si no hay respuesta en 30 min
4. **Continuar documentando** el problema hasta que se resuelva

### **Incidencias NORMALES (Prioridad Media/Baja):**
1. **Registrar** usando este formato
2. **Enviar por email** diario consolidado a final del día
3. **Seguimiento** en reunión diaria matutina

---

## 📤 CÓMO ENVIAR EL REGISTRO

### **Opción 1: Email Diario (Recomendado)**
- **Para:** carlos.mendoza@inmotech.com
- **CC:** maria.gonzalez@inmotech.com
- **Asunto:** [PILOTO] Registro Incidencias - [Tu Nombre] - [Fecha]
- **Cuerpo:** Compilar todos los registros del día en un solo email

### **Opción 2: Sistema Online (Si está disponible)**
- **URL:** https://inmotech-piloto.test.com/incidencias
- **Usuario:** [Tus credenciales de piloto]
- **Completar formulario** online directamente

### **Opción 3: WhatsApp (Solo urgencias)**
- **Carlos Mendoza:** +57 303 456 7890
- **Usar solo para incidencias críticas** que bloqueen tu trabajo

---

## 📈 MÉTRICAS DE CALIDAD DEL REPORTE

Un buen reporte de incidencia debe tener:
- ✅ **Título claro y específico**
- ✅ **Pasos para reproducir detallados**  
- ✅ **Evidencia visual** (capturas o videos)
- ✅ **Contexto del impacto** en tu trabajo
- ✅ **Información completa** en todos los campos

---

## 🎯 RECORDATORIO IMPORTANTE

**Tu feedback es CRUCIAL para el éxito de Inmotech.** 

Cada incidencia que reportes, por pequeña que parezca, contribuye a crear un sistema mejor para todos los usuarios futuros.

**¡No dudes en reportar TODO lo que notes!**

---

**Elaborado por:** María González - Líder de Proyecto  
**Validado por:** David Paredes - Soporte Técnico  
**Fecha:** 21 de octubre de 2025  
**Versión:** 1.0  

---

## 📋 PLANTILLA EN BLANCO PARA COPIAR

```
### REGISTRO DE INCIDENCIA

**INFORMACIÓN BÁSICA**
- ID de Incidencia: INC-YYYY-MM-DD-XXX
- Fecha y Hora: DD/MM/YYYY - HH:MM
- Usuario Reporta: [Tu Nombre - Tu Perfil]
- Módulo Afectado: [Módulo]
- Tipo de Incidencia: [Error/Dificultad/Sugerencia/Aspecto Positivo]
- Prioridad: [Alta/Media/Baja]

**¿QUÉ PASÓ?**
[Descripción clara]

**¿CÓMO PASÓ?**
1. [Paso 1]
2. [Paso 2]
3. [Paso 3]

**¿CUÁNDO PASÓ?**
[Frecuencia/contexto]

**IMPACTO**
- Impacto en trabajo: [Alto/Medio/Bajo]
- Frecuencia: [Siempre/Frecuente/Ocasional/Una vez]
- ¿Bloquea trabajo?: [Sí/No]
- ¿Solución temporal?: [Sí/No]

**EVIDENCIA ADJUNTA**
[Lista de archivos]

**SUGERENCIA DE SOLUCIÓN**
[Tu propuesta]
```