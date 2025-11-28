# 🎮 Tutorial Paso a Paso - Space Impact en Scratch

## 🚀 PARTE 1: CONFIGURACIÓN INICIAL (5-10 minutos)

### Paso 1: Crear el Proyecto
1. **Abrir Scratch**: Ve a [scratch.mit.edu](https://scratch.mit.edu)
2. **Clic en "Crear"**
3. **Eliminar el gato**: Clic derecho en el sprite del gato → "borrar"
4. **Cambiar fondo**: Clic en "Elegir un Fondo" → "Pintar"
   - Usar relleno negro
   - Añadir puntos blancos pequeños para estrellas
   - O elegir "Stars" de la biblioteca

### Paso 2: Crear Variables Globales
1. **Ir a "Variables"**
2. **Crear nuevas variables** (Para todos los objetos):
   - `Puntos` = 0
   - `Vidas` = 3
   - `JefeActivo` = 0
   - `VidaJefe` = 5

---

## 🚁 PARTE 2: CREAR LA NAVE (10-15 minutos)

### Paso 3: Diseñar la Nave
1. **Clic en "Elegir un Objeto" → "Pintar"**
2. **Nombrar**: "Nave"
3. **Dibujar**:
   - Triángulo apuntando hacia la derecha (azul/gris)
   - Tamaño aproximado 40x20 pixels
   - Ventanilla en el centro

### Paso 4: Programar Movimiento de la Nave
```scratch
Al presionar bandera verde
├── ir a x: [-200] y: [0]
├── fijar [Vidas v] a [3]
├── fijar [Puntos v] a [0]
├── fijar [JefeActivo v] a [0]
└── para siempre
    ├── si <¿tecla [flecha arriba v] presionada?> entonces
    │   └── cambiar y por [10]
    ├── si <¿tecla [flecha abajo v] presionada?> entonces
    │   └── cambiar y por [-10]
    ├── si <(posición y) > [160]> entonces
    │   └── fijar y a [160]
    └── si <(posición y) < [-160]> entonces
        └── fijar y a [-160]
```

### Paso 5: Programar Sistema de Disparo
```scratch
Al presionar tecla [espacio v]
├── crear clon de [Proyectil v]
└── tocar sonido [pop v]
```

**🧪 PRUEBA**: Presiona bandera verde, mueve la nave con flechas (aún no dispara, falta crear el proyectil)

---

## 🎯 PARTE 3: CREAR PROYECTILES (10-15 minutos)

### Paso 6: Diseñar Proyectil
1. **Crear nuevo sprite** → "Pintar"
2. **Nombrar**: "Proyectil"
3. **Dibujar**: Línea amarilla horizontal (10x3 pixels)

### Paso 7: Programar Proyectiles
```scratch
Al presionar bandera verde
└── esconder

al empezar como clon
├── ir a x: (posición x de [Nave v]) y: (posición y de [Nave v])
├── mostrar
└── para siempre
    ├── cambiar x por [15]
    └── si <(posición x) > [240]> entonces
        └── borrar este clon
```

**🧪 PRUEBA**: ¡Ya puedes disparar! Los proyectiles salen de la nave hacia la derecha.

---

## 🦇 PARTE 4: CREAR ENEMIGOS BÁSICOS (15-20 minutos)

### Paso 8: Diseñar Murciélago
1. **Crear nuevo sprite** → "Pintar" o buscar "Bat" en biblioteca
2. **Nombrar**: "Murciélago"
3. **Ajustar tamaño**: 50% del tamaño original

### Paso 9: Programar Generación de Enemigos
```scratch
Al presionar bandera verde
├── esconder
└── para siempre
    ├── esperar (número al azar entre [1] y [3]) segundos
    └── si <(JefeActivo) = [0]> entonces
        └── crear clon de [Murciélago v]
```

### Paso 10: Programar Movimiento del Murciélago
```scratch
al empezar como clon
├── ir a x: [240] y: (número al azar entre [-150] y [150])
├── mostrar
└── para siempre
    ├── cambiar x por [-4]
    └── si <(posición x) < [-240]> entonces
        └── borrar este clon
```

### Paso 11: Programar Colisiones Murciélago
```scratch
// Añadir al script del murciélago:
si <¿tocando [Nave v]?> entonces
├── cambiar [Vidas v] por [-1]
├── tocar sonido [chomp v]
└── borrar este clon

si <¿tocando [Proyectil v]?> entonces
├── cambiar [Puntos v] por [10]
├── tocar sonido [pop v]
└── borrar este clon
```

### Paso 12: Actualizar Proyectiles para Destruir Murciélagos
```scratch
// Añadir al script del proyectil:
si <¿tocando [Murciélago v]?> entonces
├── cambiar [Puntos v] por [10]
├── tocar sonido [pop v]
└── borrar este clon
```

**🧪 PRUEBA**: ¡Ya tienes enemigos! Puedes disparar y destruir murciélagos, ganar puntos y perder vidas.

---

## 🪳 PARTE 5: CREAR JEFE FINAL (20-25 minutos)

### Paso 13: Diseñar la Cucaracha
1. **Crear nuevo sprite** → "Pintar"
2. **Nombrar**: "Cucaracha"
3. **Dibujar**: Forma ovalada marrón/negra más grande que el murciélago
4. **Añadir detalles**: Antenas, patas, ojos rojos

### Paso 14: Programar Aparición del Jefe
```scratch
Al presionar bandera verde
├── esconder
└── para siempre
    └── si <<(Puntos) > [100]> y <(JefeActivo) = [0]>> entonces
        ├── fijar [JefeActivo v] a [1]
        ├── fijar [VidaJefe v] a [5]
        ├── decir [¡¡¡JEFE FINAL!!!] por [2] segundos
        └── crear clon de [Cucaracha v]
```

### Paso 15: Programar Movimiento del Jefe
```scratch
al empezar como clon
├── ir a x: [200] y: [0]
├── mostrar
└── para siempre
    ├── cambiar y por (número al azar entre [-8] y [8])
    ├── cambiar x por [-1]
    ├── si <(posición y) > [150]> entonces
    │   └── fijar y a [150]
    ├── si <(posición y) < [-150]> entonces
    │   └── fijar y a [-150]
    └── si <(posición x) < [50]> entonces
        └── fijar x a [200]
```

### Paso 16: Programar Sistema de Vida del Jefe
```scratch
// Añadir al script del jefe:
si <¿tocando [Proyectil v]?> entonces
├── cambiar [VidaJefe v] por [-1]
├── decir (unir [Vida: ] (VidaJefe)) por [1] segundos
└── tocar sonido [pop v]

si <(VidaJefe) ≤ [0]> entonces
├── cambiar [Puntos v] por [100]
├── fijar [JefeActivo v] a [0]
├── decir [¡JEFE DERROTADO!] por [3] segundos
└── borrar este clon

si <¿tocando [Nave v]?> entonces
├── cambiar [Vidas v] por [-2]
├── tocar sonido [chomp v]
└── borrar este clon
```

### Paso 17: Actualizar Proyectiles para Dañar al Jefe
```scratch
// Añadir al script del proyectil:
si <¿tocando [Cucaracha v]?> entonces
├── cambiar [VidaJefe v] por [-1]
├── tocar sonido [pop v]
└── borrar este clon
```

**🧪 PRUEBA**: ¡Consigue 100 puntos y aparecerá el jefe! Necesitas 5 disparos para derrotarlo.

---

## 💀 PARTE 6: SISTEMA DE GAME OVER (10-15 minutos)

### Paso 18: Programar Game Over en la Nave
```scratch
para siempre
└── si <(Vidas) < [1]> entonces
    ├── decir [GAME OVER - Presiona R para reiniciar] por [3] segundos
    ├── parar [otros programas del objeto v]
    └── esperar hasta que <¿tecla [r v] presionada?>
        ├── fijar [Vidas v] a [3]
        ├── fijar [Puntos v] a [0]
        ├── fijar [JefeActivo v] a [0]
        └── ir a x: [-200] y: [0]
```

---

## 🎆 PARTE 7: EFECTOS Y MEJORAS (15-20 minutos)

### Paso 19: Crear Explosiones (Opcional)
1. **Crear sprite "Explosión"**
2. **Diseño**: Círculo con rayos, colores brillantes
3. **Script**:
```scratch
Al presionar bandera verde
└── esconder

al empezar como clon
├── mostrar
├── repetir [8] veces
│   ├── cambiar tamaño por [10]
│   ├── cambiar efecto [color v] por [25]
│   └── esperar [0.1] segundos
└── borrar este clon
```

### Paso 20: Añadir Explosiones a Colisiones
```scratch
// En murciélago y cucaracha, cuando son destruidos:
crear clon de [Explosión v]
```

### Paso 21: Mostrar Información en Pantalla
1. **Clic en Variables**
2. **Marcar** "Puntos" y "Vidas" para mostrarlas
3. **Posicionarlas** en esquina superior

---

## 🎵 PARTE 8: SONIDOS Y MÚSICA (5-10 minutos)

### Paso 22: Añadir Sonidos
1. **Para cada sprite**: Ir a "Sonidos" → "Elegir un Sonido"
2. **Sonidos recomendados**:
   - **Nave disparando**: "Laser" o "Pop"
   - **Explosiones**: "Boom" o "Chomp"
   - **Música de fondo**: "Space Melody"

### Paso 23: Crear Controlador de Música
```scratch
Al presionar bandera verde
└── para siempre
    ├── tocar sonido [space melody v] hasta que termine
    └── esperar [1] segundos
```

---

## ✅ CHECKLIST FINAL

### Funcionalidades Básicas
- [ ] Nave se mueve arriba/abajo con flechas
- [ ] Disparo con barra espaciadora
- [ ] Murciélagos aparecen desde la derecha
- [ ] Colisiones funcionan (nave-enemigo, proyectil-enemigo)
- [ ] Sistema de puntos y vidas
- [ ] Jefe aparece después de 100 puntos
- [ ] Game Over y reinicio

### Mejoras Opcionales
- [ ] Explosiones cuando destruyes enemigos
- [ ] Sonidos para acciones
- [ ] Música de fondo
- [ ] Efectos visuales (brillos, colores)
- [ ] Diferentes niveles de dificultad
- [ ] Power-ups especiales

---

## 🎮 ¡CONTROLES FINALES!
- **↑/↓**: Mover nave
- **Espacio**: Disparar
- **R**: Reiniciar (después de Game Over)
- **Bandera Verde**: Iniciar juego

## 🏆 ¡MISIÓN CUMPLIDA!
¡Felicitaciones! Has recreado el clásico Space Impact del Nokia 3310 en Scratch. 

**Desafíos adicionales**:
- Añade más tipos de enemigos
- Crea diferentes armas
- Implementa power-ups
- Añade múltiples niveles
- Crea un menú principal

¡Diviértete jugando y mejorando tu Space Impact!