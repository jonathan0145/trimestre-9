# 🚀 Código Completo para Sprites - Space Impact Scratch

## 🎮 SPRITE 1: NAVE (Player)

### Variables del Sprite
- Sin variables específicas (usa variables globales)

### Scripts de la Nave

#### Script Principal - Movimiento
```scratch
Al presionar bandera verde
│
├── ir a x: -200 y: 0
├── fijar [Vidas v] a [3]
├── fijar [Puntos v] a [0]
├── fijar [JefeActivo v] a [0]
├── fijar [VidaJefe v] a [5]
│
└── para siempre
    │
    ├── si <¿tecla [flecha arriba v] presionada?> entonces
    │   └── cambiar y por [10]
    │
    ├── si <¿tecla [flecha abajo v] presionada?> entonces
    │   └── cambiar y por [-10]
    │
    ├── si <(posición y) > [160]> entonces
    │   └── fijar y a [160]
    │
    └── si <(posición y) < [-160]> entonces
        └── fijar y a [-160]
```

#### Script de Disparo
```scratch
Al presionar tecla [espacio v]
│
├── crear clon de [Proyectil v]
└── tocar sonido [Laser v]
```

#### Script de Game Over
```scratch
para siempre
│
└── si <(Vidas) < [1]> entonces
    │
    ├── decir [GAME OVER - Presiona R para reiniciar] por [3] segundos
    ├── parar [otros programas del objeto v]
    │
    └── esperar hasta que <¿tecla [r v] presionada?>
        │
        ├── fijar [Vidas v] a [3]
        ├── fijar [Puntos v] a [0]
        ├── fijar [JefeActivo v] a [0]
        ├── ir a x: [-200] y: [0]
        └── al presionar bandera verde
```

---

## 🎯 SPRITE 2: PROYECTIL (Laser)

### Scripts del Proyectil

#### Script de Inicialización
```scratch
Al presionar bandera verde
│
└── esconder
```

#### Script del Clon de Proyectil
```scratch
al empezar como clon
│
├── ir a x: (posición x de [Nave v]) y: (posición y de [Nave v])
├── mostrar
├── apuntar hacia [90 v] grados
│
├── para siempre
│   │
│   ├── cambiar x por [15]
│   │
│   └── si <(posición x) > [240]> entonces
│       └── borrar este clon
│
├── si <¿tocando [Murciélago v]?> entonces
│   │
│   ├── cambiar [Puntos v] por [10]
│   ├── tocar sonido [pop v]
│   └── borrar este clon
│
└── si <¿tocando [Cucaracha v]?> entonces
    │
    ├── cambiar [VidaJefe v] por [-1]
    ├── tocar sonido [pop v]
    └── borrar este clon
```

---

## 🦇 SPRITE 3: MURCIÉLAGO (Enemigo Básico)

### Scripts del Murciélago

#### Script Generador de Enemigos
```scratch
Al presionar bandera verde
│
└── para siempre
    │
    ├── esperar (número al azar entre [1] y [3]) segundos
    │
    └── si <(JefeActivo) = [0]> entonces
        │
        └── crear clon de [Murciélago v]
```

#### Script Principal del Murciélago
```scratch
Al presionar bandera verde
│
└── esconder
```

#### Script del Clon del Murciélago
```scratch
al empezar como clon
│
├── ir a x: [240] y: (número al azar entre [-150] y [150])
├── mostrar
├── fijar tamaño a [50] %
│
├── para siempre
│   │
│   ├── cambiar x por [-4]
│   ├── cambiar y por (número al azar entre [-2] y [2])
│   │
│   └── si <(posición x) < [-240]> entonces
│       └── borrar este clon
│
├── si <¿tocando [Nave v]?> entonces
│   │
│   ├── cambiar [Vidas v] por [-1]
│   ├── tocar sonido [Chomp v]
│   └── borrar este clon
│
└── si <¿tocando [Proyectil v]?> entonces
    │
    ├── crear clon de [Explosión v]
    └── borrar este clon
```

---

## 🪳 SPRITE 4: CUCARACHA (Jefe Final)

### Scripts de la Cucaracha

#### Script Controlador del Jefe
```scratch
Al presionar bandera verde
│
├── esconder
│
└── para siempre
    │
    └── si <<(Puntos) > [100]> y <(JefeActivo) = [0]>> entonces
        │
        ├── fijar [JefeActivo v] a [1]
        ├── fijar [VidaJefe v] a [5]
        ├── decir [¡¡¡JEFE FINAL!!!] por [2] segundos
        └── crear clon de [Cucaracha v]
```

#### Script del Clon del Jefe
```scratch
al empezar como clon
│
├── ir a x: [200] y: (número al azar entre [-100] y [100])
├── mostrar
├── fijar tamaño a [80] %
│
├── para siempre
│   │
│   ├── cambiar y por (número al azar entre [-8] y [8])
│   ├── cambiar x por [-1]
│   │
│   ├── si <(posición y) > [150]> entonces
│   │   └── fijar y a [150]
│   │
│   ├── si <(posición y) < [-150]> entonces
│   │   └── fijar y a [-150]
│   │
│   ├── si <(posición x) < [50]> entonces
│   │   └── fijar x a [200]
│   │
│   ├── si <(VidaJefe) ≤ [0]> entonces
│   │   │
│   │   ├── cambiar [Puntos v] por [100]
│   │   ├── fijar [JefeActivo v] a [0]
│   │   ├── decir [¡JEFE DERROTADO!] por [3] segundos
│   │   └── borrar este clon
│   │
│   └── esperar [2] segundos
│       └── crear clon de [ProyectilJefe v]
│
├── si <¿tocando [Nave v]?> entonces
│   │
│   ├── cambiar [Vidas v] por [-2]
│   ├── tocar sonido [Chomp v]
│   └── borrar este clon
│
└── si <¿tocando [Proyectil v]?> entonces
    │
    ├── crear clon de [Explosión v]
    ├── decir (unir [Vida: ] (VidaJefe)) por [1] segundos
    └── borrar este clon
```

---

## 💥 SPRITE 5: EXPLOSIÓN (Efectos)

### Scripts de la Explosión

#### Script de Inicialización
```scratch
Al presionar bandera verde
│
└── esconder
```

#### Script del Clon de Explosión
```scratch
al empezar como clon
│
├── mostrar
├── fijar tamaño a [30] %
├── tocar sonido [Boom v]
│
├── repetir [8] veces
│   │
│   ├── cambiar tamaño por [10]
│   ├── cambiar efecto [color v] por [25]
│   └── esperar [0.1] segundos
│
└── borrar este clon
```

---

## 🎯 SPRITE 6: PROYECTIL DEL JEFE

### Scripts del Proyectil del Jefe

#### Script de Inicialización
```scratch
Al presionar bandera verde
│
└── esconder
```

#### Script del Clon del Proyectil del Jefe
```scratch
al empezar como clon
│
├── ir a x: (posición x de [Cucaracha v]) y: (posición y de [Cucaracha v])
├── mostrar
├── fijar tamaño a [20] %
├── apuntar hacia [Nave v]
│
├── para siempre
│   │
│   ├── cambiar x por [-8]
│   │
│   └── si <(posición x) < [-240]> entonces
│       └── borrar este clon
│
└── si <¿tocando [Nave v]?> entonces
    │
    ├── cambiar [Vidas v] por [-1]
    ├── tocar sonido [Chomp v]
    └── borrar este clon
```

---

## 🎵 SPRITE 7: CONTROLADOR DE MÚSICA (Opcional)

### Script de Música de Fondo
```scratch
Al presionar bandera verde
│
└── para siempre
    │
    ├── tocar sonido [space music v] hasta que termine
    └── esperar [1] segundos
```

---

## 📊 Variables Globales Necesarias
- **Puntos** (Para todos los objetos) = 0
- **Vidas** (Para todos los objetos) = 3  
- **JefeActivo** (Para todos los objetos) = 0
- **VidaJefe** (Para todos los objetos) = 5

## 🎨 Diseño de Sprites Sugerido
- **Nave**: Triángulo gris/azul apuntando a la derecha
- **Proyectil**: Línea amarilla pequeña
- **Murciélago**: Forma de murciélago negro/morado
- **Cucaracha**: Forma ovalada marrón/negra más grande
- **Explosión**: Círculo con rayos, colores brillantes
- **Fondo**: Negro con puntos blancos (estrellas)

¡Con estos scripts tendrás un Space Impact completamente funcional en Scratch!