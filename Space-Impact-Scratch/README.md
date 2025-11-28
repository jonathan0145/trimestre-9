# 🚀 Space Impact - Scratch Edition

## 📱 Recreación del clásico Nokia 3310

### 🎮 Descripción del Juego
Space Impact es una recreación del icónico juego del Nokia 3310, desarrollado en Scratch. Controla tu nave espacial, dispara a enemigos y enfréntate al jefe final.

### 🎯 Características del Juego
- **Nave espacial**: Movimiento vertical con flechas arriba/abajo
- **Disparos**: Barra espaciadora para disparar proyectiles
- **Enemigos**: Murciélagos que vuelan desde la derecha
- **Jefe Final**: Cucaracha con patrones de ataque especiales
- **Sistema de puntos**: 10 puntos por murciélago, 100 puntos por jefe
- **Vidas**: 3 vidas iniciales
- **Efectos**: Explosiones y sonidos retro

### 🎭 Sprites Necesarios
1. **Nave** (Player) - Nave espacial azul/gris
2. **Proyectil** - Láser de la nave
3. **Murciélago** - Enemigo básico
4. **Cucaracha** - Jefe final
5. **Explosión** - Efecto visual
6. **Fondo** - Espacio estrellado negro

### 🕹️ Controles
- **↑ Flecha Arriba**: Mover nave hacia arriba
- **↓ Flecha Abajo**: Mover nave hacia abajo
- **Barra Espaciadora**: Disparar proyectiles
- **Bandera Verde**: Iniciar juego
- **R**: Reiniciar juego (Game Over)

### 📋 Instrucciones de Creación

#### 1. Preparar Sprites
- Crear o importar sprites de nave, murciélago y cucaracha
- Diseñar proyectiles simples (rectángulos pequeños)
- Crear animación de explosión

#### 2. Programación Principal
- Movimiento de nave limitado al borde izquierdo
- Sistema de disparos automáticos
- Generación aleatoria de enemigos
- Detección de colisiones
- Sistema de vidas y puntuación

#### 3. Jefe Final
- Aparece después de cierta puntuación
- Más vida que enemigos normales
- Patrones de movimiento complejos
- Dispara proyectiles hacia la nave

### 🎵 Sonidos Recomendados
- **Disparo**: Sonido corto y agudo
- **Explosión**: Sonido grave y fuerte
- **Música de fondo**: Tema espacial repetitivo
- **Game Over**: Sonido dramático

### 📊 Variables del Juego
- `Puntos`: Puntuación actual
- `Vidas`: Vidas restantes (3 inicial)
- `JefeActivo`: Controla si el jefe está en pantalla
- `VidaJefe`: Vida del jefe final (5 hits)
- `Velocidad`: Velocidad de enemigos (aumenta gradualmente)

### 🚀 ¡Cómo Empezar!
1. Abre Scratch (scratch.mit.edu)
2. Crea un nuevo proyecto
3. Elimina el sprite del gato
4. Sigue las instrucciones paso a paso abajo

---

## 📝 Guía Paso a Paso para Scratch

### PASO 1: Configuración Inicial
1. **Crear fondo**: Fondo negro con estrellas
2. **Variables globales**: 
   - Puntos = 0
   - Vidas = 3
   - JefeActivo = 0
   - VidaJefe = 5

### PASO 2: Sprite de la Nave
```
Al presionar bandera verde:
- Ir a x(-200) y(0)
- Fijar Vidas a 3
- Fijar Puntos a 0

Para siempre:
- Si tecla "flecha arriba" presionada entonces
  - cambiar y por 10
- Si tecla "flecha abajo" presionada entonces
  - cambiar y por -10
- Si y > 160 entonces
  - fijar y a 160
- Si y < -160 entonces
  - fijar y a -160

Al presionar tecla "espacio":
- Crear clon de "Proyectil"
```

### PASO 3: Sprite del Proyectil
```
Al presionar bandera verde:
- Esconder

Al empezar como clon:
- Ir a (x de Nave) (y de Nave)
- Mostrar
- Para siempre:
  - cambiar x por 10
  - Si x > 240 entonces
    - borrar este clon

Si toca "Murciélago" entonces:
- cambiar Puntos por 10
- borrar este clon

Si toca "Cucaracha" entonces:
- cambiar VidaJefe por -1
- borrar este clon
```

### PASO 4: Sprite del Murciélago
```
Al presionar bandera verde:
- Para siempre:
  - esperar (número al azar entre 1 y 3) segundos
  - Si JefeActivo = 0 entonces
    - crear clon de "Murciélago"

Al empezar como clon:
- Ir a x(240) y(número al azar entre -150 y 150)
- Mostrar
- Para siempre:
  - cambiar x por -5
  - Si x < -240 entonces
    - borrar este clon
  - Si toca "Nave" entonces
    - cambiar Vidas por -1
    - borrar este clon
```

### PASO 5: Sprite de la Cucaracha (Jefe)
```
Al presionar bandera verde:
- Esconder
- Para siempre:
  - Si Puntos > 100 y JefeActivo = 0 entonces
    - fijar JefeActivo a 1
    - fijar VidaJefe a 5
    - crear clon de "Cucaracha"

Al empezar como clon:
- Ir a x(200) y(0)
- Mostrar
- Para siempre:
  - cambiar y por (número al azar entre -10 y 10)
  - cambiar x por -2
  - Si VidaJefe ≤ 0 entonces
    - cambiar Puntos por 100
    - fijar JefeActivo a 0
    - borrar este clon
  - Si toca "Nave" entonces
    - cambiar Vidas por -2
    - borrar este clon
```

¿Quieres que continúe con más detalles específicos de programación o prefieres que te guíe en algún aspecto particular del juego?