# 🚀 INSTRUCCIONES PARA IMPORTAR EL JUEGO

## 📁 Método 1: Importar JSON a Scratch (RECOMENDADO)

### PASO 1: Preparar el archivo
1. **Archivo creado**: `space-impact.json`
2. **Ubicación**: `Space-Impact-Scratch/space-impact.json`

### PASO 2: Convertir JSON a SB3
Scratch usa archivos `.sb3`, pero podemos convertir:

1. **Visita**: [scratch.mit.edu](https://scratch.mit.edu)
2. **Clic en "Crear"**
3. **Ve al menú "Archivo"** → **"Cargar desde tu computadora"**
4. **NOTA**: Como Scratch no acepta JSON directamente, necesitamos:

---

## 🛠️ Método 2: CREAR MANUALMENTE (100% FUNCIONAL)

Sigue estos pasos exactos para tener el juego funcionando en 15 minutos:

### PASO 1: Configuración Inicial
1. **Abrir**: [scratch.mit.edu](https://scratch.mit.edu) → **"Crear"**
2. **Eliminar** el sprite del gato (clic derecho → borrar)
3. **Fondo**: Clic en "Elegir un Fondo" → **"Stars"** o pintar fondo negro

### PASO 2: Crear Variables
**Instrucciones detalladas**:
1. **Clic en "Variables"** (sección naranja en la izquierda)
2. **Clic en "Crear una Variable"**
3. **Crear estas 4 variables** (una por una):

**Variables a crear** (Para todos los objetos):
- `Puntos` = 0
- `Vidas` = 3  
- `JefeActivo` = 0
- `VidaJefe` = 5

**Cómo crear cada variable**:
- Nombre: `Vidas` → Seleccionar "Para todos los objetos" → "Aceptar"
- Nombre: `Puntos` → Seleccionar "Para todos los objetos" → "Aceptar"  
- Nombre: `JefeActivo` → Seleccionar "Para todos los objetos" → "Aceptar"
- Nombre: `VidaJefe` → Seleccionar "Para todos los objetos" → "Aceptar"

### PASO 3: Sprite NAVE 🚁
**Crear sprite** → **"Pintar"** → Nombre: **"Nave"**
**Dibujar**: Triángulo azul apuntando a la derecha

**CÓDIGO DE LA NAVE**:
```scratch
🏁 Al presionar bandera verde
├── ir a x: -200 y: 0
├── fijar [Vidas v] a [3]     ← Bloque NARANJA de Variables
├── fijar [Puntos v] a [0]    ← Bloque NARANJA de Variables  
├── fijar [JefeActivo v] a [0] ← Bloque NARANJA de Variables
└── para siempre
    ├── si <tecla "flecha arriba" presionada> entonces
    │   └── cambiar y por 10
    ├── si <tecla "flecha abajo" presionada> entonces
    │   └── cambiar y por -10
    ├── si <y > 160> entonces fijar y a 160
    └── si <y < -160> entonces fijar y a -160

**📋 Cómo crear "si <y > 160> entonces fijar y a 160":**

**PASO A: Crear el bloque SI**
1. **Control** (sección amarilla) → Arrastrar bloque: `si <> entonces`

**PASO B: Crear la condición "y > 160"** 
1. **Operadores** (sección verde) → Arrastrar: `[] > []`
2. **Movimiento** (sección azul) → Arrastrar: `(posición y)`
3. **Colocar** `(posición y)` en el primer espacio del `[]> []`
4. **Escribir** `160` en el segundo espacio
5. **Resultado**: `<posición y > 160>`

**PASO C: Completar la acción "fijar y a 160"** ⭐ **DETALLADO**

**🔍 UBICACIÓN EN SCRATCH:**
1. **Lado izquierdo** → Buscar sección **"Movimiento"** (color azul)
2. **Buscar el bloque**: `fijar y a [0]` (es un bloque rectangular azul)

**🖱️ ARRASTRAR EL BLOQUE:**
1. **Hacer clic** sobre el bloque `fijar y a [0]` 
2. **Mantener presionado** el botón del mouse
3. **Arrastrar hacia la derecha** al área de código

**📍 COLOCAR DENTRO DEL "si entonces":**
- **VER**: El bloque `si <> entonces` tiene una **forma de "C"**
- **OBJETIVO**: Colocar `fijar y a [160]` **DENTRO** de esa "C"
- **VISUAL**: 
```
┌─ si <condición> entonces ─┐
│  ← AQUÍ va "fijar y a [160]"  │
└─────────────────────────────┘
```

**✏️ CAMBIAR EL NÚMERO:**
1. **Hacer clic** en el número `0` del bloque `fijar y a [0]`
2. **Borrar el 0** y **escribir 160**
3. **Presionar Enter** para confirmar

**✅ RESULTADO FINAL:**
```
si <posición y > 160> entonces
    fijar y a [160]  ← Este bloque debe estar DENTRO
```

**🎯 TIP VISUAL:** 
- El bloque `fijar y a [160]` debe quedar **indentado** (más a la derecha)
- Si está bien colocado, se verá como un "escalón" dentro del bloque "si"

**PASO D: Repetir para la segunda condición**
- Mismo proceso pero con `<posición y < -160>` y `fijar y a [-160]`
- **Operador**: `[] < []` (menor que)
- **Valores**: `posición y` < `-160` → `fijar y a [-160]`

⌨️ Al presionar tecla "espacio"
└── crear clon de "Proyectil"

**📋 Cómo crear el bloque de DISPARO:**

**PASO 1: Evento de tecla**
1. **Eventos** (sección marrón/dorada) → `al presionar tecla [espacio v]`

**PASO 2: Crear clon**
1. **Control** (amarillo) → `crear clon de [mi objeto v]`
2. **Clic en la flecha ▼** → Seleccionar **"Proyectil"**

**✅ RESULTADO:**
```
🟫 al presionar tecla [espacio v]
🟨 crear clon de [Proyectil v]
```

**🎯 IMPORTANTE:** 
- Este bloque va **SEPARADO**, NO dentro del "para siempre"
- Va en el mismo sprite **"Nave"**
- Debe estar al **mismo nivel** que el bloque "Al presionar bandera verde"
```

**📋 Cómo encontrar "fijar Vidas a 3":**
1. **Variables** (sección naranja) → Buscar bloque: `fijar [mi variable v] a [0]`
2. **Clic en la flecha** del bloque → Seleccionar **"Vidas"**
3. **Cambiar el 0** por **3**
4. **Resultado**: `fijar [Vidas v] a [3]`

### PASO 4: Sprite PROYECTIL 🎯
**Crear sprite** → **"Pintar"** → Nombre: **"Proyectil"**
**Dibujar**: Línea amarilla horizontal (10x3 pixels)

**CÓDIGO DEL PROYECTIL**:
```scratch
🏁 Al presionar bandera verde
└── esconder

🎭 al empezar como clon
├── ir a x: (posición x de Nave) y: (posición y de Nave)
├── mostrar
└── para siempre
    ├── cambiar x por 15
    ├── si <x > 240> entonces borrar este clon

**📋 Cómo completar "si <x > 240> entonces borrar este clon":**

**✅ LO QUE YA TIENES CORRECTO:**
- 🟨 Bloque `si <> entonces` (Control - amarillo)
- 🟩 Operador `[] > []` con `240` (Operadores - verde)

**📍 LO QUE FALTA AGREGAR:**

**PASO 1: Completar la condición**
1. **Movimiento** (azul) → Buscar: `(posición x)`
2. **Arrastrar** `(posición x)` al **primer círculo** del `[] > 240`
3. **Resultado**: `<posición x > 240>`

**PASO 2: Agregar la acción "borrar este clon"**
1. **Control** (amarillo) → Buscar: `borrar este clon`
2. **Arrastrar** dentro del bloque `si entonces` (en la parte interna)

**✅ RESULTADO FINAL:**
```
🟨 si <🔵(posición x) 🟩> 240> entonces
    🟨 borrar este clon  ← Debe estar DENTRO
```

**🎯 ¿QUÉ HACE ESTE BLOQUE?**
- Cuando el proyectil llega al **borde derecho** (x=240)
- **Elimina** el proyectil para no saturar la memoria
- **Optimiza** el rendimiento del juego
    ├── si <tocando Murciélago> entonces
    │   ├── cambiar Puntos por 10
    │   └── borrar este clon
    └── si <tocando Cucaracha> entonces
        ├── cambiar Puntos por 10
        └── borrar este clon

**📋 Cómo crear "si <tocando Murciélago> entonces":**

**PASO A: Crear el bloque SI**
1. **Control** (sección amarilla) → Arrastrar: `si <> entonces`

**PASO B: Crear la condición "tocando Murciélago"**
1. **Sensores** (sección azul claro) → Buscar: `¿tocando [mouse-pointer v]?`
2. **Clic en la flecha ▼** del bloque → Cambiar por **"Murciélago"**
3. **Arrastrar** este bloque al rombo del `si <> entonces`
4. **Resultado**: `<¿tocando Murciélago?>`

**PASO C: Agregar "cambiar Puntos por 10"**
1. **Variables** (naranja) → `cambiar [mi variable v] por [1]`
2. **Clic en la flecha ▼** → Seleccionar **"Puntos"**
3. **Cambiar el 1** por **10**
4. **Arrastrar DENTRO** del bloque `si entonces`

**PASO D: Agregar "borrar este clon"**
1. **Control** (amarillo) → `borrar este clon`
2. **Arrastrar DENTRO** del bloque `si entonces` (debajo del anterior)

**✅ RESULTADO VISUAL MURCIÉLAGO:**
```
🟨 si <🔵¿tocando Murciélago?> entonces ┐
    🟠 cambiar [Puntos v] por [10]      │ ← AMBOS bloques 
    🟨 borrar este clon                 │   DENTRO del "si"
                                        └─
```

**📋 Cómo crear "si <tocando Cucaracha> entonces":**

**PASO E: Crear segundo bloque SI**
1. **Control** (amarilla) → Arrastrar: `si <> entonces`
2. **Sensores** (azul claro) → `¿tocando [Cucaracha v]?`
3. **Variables** (naranja) → `cambiar [Puntos v] por [10]`
4. **Control** (amarillo) → `borrar este clon`

**✅ RESULTADO VISUAL CUCARACHA:**
```
🟨 si <🔵¿tocando Cucaracha?> entonces ┐
    🟠 cambiar [Puntos v] por [10]     │ ← PROYECTIL solo da puntos
    🟨 borrar este clon                │   CUCARACHA maneja su vida
                                       └─
```

**🎯 DIVISIÓN DE RESPONSABILIDADES:**
- **PROYECTIL** → Solo suma puntos y se elimina
- **CUCARACHA** → Solo maneja su propia vida (`VidaJefe por -1`)
```

### PASO 5: Sprite MURCIÉLAGO 🦇
**Crear sprite** → Buscar **"Bat"** o pintar murciélago
**Nombre**: **"Murciélago"** → **Tamaño**: 60%

**CÓDIGO DEL MURCIÉLAGO**:
```scratch
🏁 Al presionar bandera verde
├── esconder
└── para siempre
    ├── esperar (número al azar entre 1 y 3) segundos
    └── si <JefeActivo = 0> entonces
        └── crear clon de "Murciélago"

🎭 al empezar como clon
├── ir a x: 240 y: (número al azar entre -150 y 150)
├── mostrar
├── para siempre
│   ├── cambiar x por -4
│   └── si <x < -240> entonces borrar este clon
├── si <tocando Nave> entonces
│   ├── cambiar Vidas por -1
│   └── borrar este clon
└── si <tocando Proyectil> entonces
    └── borrar este clon
```

### PASO 6: Sprite CUCARACHA 🪳
**Crear sprite** → **"Pintar"** → Nombre: **"Cucaracha"**
**Dibujar**: Óvalo marrón con antenas → **Tamaño**: 80%

**CÓDIGO DE LA CUCARACHA**:
```scratch
🏁 Al presionar bandera verde
├── esconder
└── para siempre
    └── si <Puntos > 100 Y JefeActivo = 0> entonces
        ├── fijar JefeActivo a 1
        ├── fijar VidaJefe a 5
        ├── decir "¡¡¡JEFE FINAL!!!" por 2 segundos
        └── crear clon de "Cucaracha"

🎭 al empezar como clon
├── ir a x: 200 y: 0
├── mostrar
├── para siempre
│   ├── cambiar y por (número al azar entre -8 y 8)
│   ├── cambiar x por -1
│   ├── si <y > 150> entonces fijar y a 150
│   ├── si <y < -150> entonces fijar y a -150
│   ├── si <x < 50> entonces fijar x a 200
│   └── si <VidaJefe ≤ 0> entonces
│       ├── cambiar Puntos por 100
│       ├── fijar JefeActivo a 0
│       ├── decir "¡JEFE DERROTADO!" por 3 segundos
│       └── borrar este clon
├── si <tocando Nave> entonces
│   ├── cambiar Vidas por -2
│   └── borrar este clon
└── si <tocando Proyectil> entonces
    ├── cambiar VidaJefe por -1
    └── decir (unir "Vida: " VidaJefe) por 1 segundos
```

### PASO 7: Mostrar Variables
**Variables** → Marcar:
- ✅ **Puntos** (aparece arriba izquierda)
- ✅ **Vidas** (aparece abajo de Puntos)

---

## 🔧 **GUÍA RÁPIDA DE BLOQUES**

### **Variables (Bloques NARANJAS)**
- `fijar [mi variable v] a [0]` → Para inicializar variables
- `cambiar [mi variable v] por [1]` → Para sumar/restar valores
- `(mi variable)` → Para leer el valor de una variable

### **Movimiento (Bloques AZULES)**  
- `ir a x: [0] y: [0]` → Posicionar sprite
- `cambiar x por [10]` / `cambiar y por [10]` → Mover sprite
- `(posición x)` / `(posición y)` → Leer posición actual

### **Control (Bloques AMARILLOS)**
- `para siempre` → Bucle infinito
- `si <> entonces` → Condición
- `crear clon de [mi objeto v]` → Duplicar sprite
- `al empezar como clon` → Código para clones

### **Sensores (Bloques AZUL CLARO)**
- `¿tecla [espacio v] presionada?` → Detectar tecla
- `¿tocando [mi objeto v]?` → Detectar colisión
- `(posición x de [mi objeto v])` → Posición de otro sprite

---

## ✅ VERIFICACIÓN FINAL

### ¿Funciona correctamente?
- [ ] **Nave se mueve** arriba/abajo con flechas
- [ ] **Dispara proyectiles** con espacio
- [ ] **Murciélagos aparecen** desde la derecha
- [ ] **Puntos aumentan** al destruir murciélagos (+10)
- [ ] **Vidas disminuyen** al tocar enemigos (-1)
- [ ] **Jefe aparece** después de 100 puntos
- [ ] **Jefe necesita 5 disparos** para ser derrotado
- [ ] **Variables se muestran** en pantalla

### 🎮 Controles
- **↑/↓**: Mover nave
- **Espacio**: Disparar
- **Bandera Verde**: Empezar/Reiniciar

---

## 🚀 PROYECTO COMPLETO EN 15 MINUTOS

**Tiempo estimado por sprite**:
- ⚙️ Configuración inicial: 3 min
- 🚁 Nave: 3 min  
- 🎯 Proyectil: 3 min
- 🦇 Murciélago: 4 min
- 🪳 Cucaracha: 5 min
- ✅ Verificaciones: 2 min

**Total: ~20 minutos para una versión completamente funcional**

---

## 💡 CONSEJOS ÚTILES

1. **Copia exactamente** los valores numéricos (velocidades, posiciones)
2. **Nombres de sprites** deben coincidir exactamente
3. **Variables** deben existir antes de usarlas
4. **Prueba cada sprite** después de programarlo
5. **Si algo no funciona**: Revisa nombres y valores

---

## 🎯 ¿PROBLEMAS?

### La nave no se mueve
- ✅ Verifica que el código esté en el sprite "Nave"
- ✅ Revisa que sea "para siempre" y no "repetir X veces"

### Los proyectiles no aparecen
- ✅ Asegúrate que el sprite se llame exactamente "Proyectil"
- ✅ Verifica que tenga el código "al empezar como clon"

### Los enemigos no aparecen
- ✅ El sprite debe llamarse "Murciélago" 
- ✅ Debe tener "esconder" al presionar bandera verde

### El jefe no aparece
- ✅ Consigue exactamente 100 puntos primero
- ✅ Revisa que JefeActivo se muestre como 0

---

## 🏆 ¡LISTO PARA JUGAR!

Una vez completado, tendrás una **recreación perfecta** del Space Impact del Nokia 3310 en Scratch, ¡completamente funcional y jugable!

**¿Necesitas ayuda con algún paso específico?** Consulta los archivos:
- `TUTORIAL-COMPLETO.md` - Tutorial detallado paso a paso
- `CODIGOS-SPRITES.md` - Código completo de cada sprite
- `GUIA-SPRITES.md` - Diseño visual de personajes