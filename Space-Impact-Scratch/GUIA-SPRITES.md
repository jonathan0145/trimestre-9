# 🎨 Guía de Sprites y Disfraces - Space Impact

## 🚀 SPRITE 1: NAVE ESPACIAL

### Disfraz 1: Nave Principal
```
Forma: Triángulo apuntando hacia la derecha
Color: Azul claro o gris
Tamaño: 40x20 pixels aproximadamente
Detalles: 
- Ventanilla azul en el centro
- Líneas de detalle en gris oscuro
- Propulsor en la parte trasera (círculo pequeño)
```

### Disfraz 2: Nave con Propulsor Activo
```
Igual a la nave principal pero con:
- Llama naranja/amarilla saliendo del propulsor
- Para efecto de animación opcional
```

---

## 🎯 SPRITE 2: PROYECTIL

### Disfraz Único: Láser
```
Forma: Línea/rectángulo horizontal pequeño
Color: Amarillo brillante o blanco
Tamaño: 10x3 pixels
Efecto: Puede tener un brillo sutil
```

---

## 🦇 SPRITE 3: MURCIÉLAGO (Enemigo)

### Disfraz 1: Alas Arriba
```
Forma: Murciélago con alas hacia arriba
Color: Negro o morado oscuro
Tamaño: 30x25 pixels
Detalles:
- Ojos rojos pequeños
- Alas con forma puntiaguda
- Cuerpo ovalado pequeño
```

### Disfraz 2: Alas Abajo
```
Igual al anterior pero con alas hacia abajo
Para crear efecto de aleteo:
- Alternar entre disfraz 1 y 2 cada 0.2 segundos
```

---

## 🪳 SPRITE 4: CUCARACHA (Jefe Final)

### Disfraz 1: Cucaracha Normal
```
Forma: Ovalada alargada
Color: Marrón oscuro o negro
Tamaño: 60x40 pixels
Detalles:
- Antenas largas en la parte frontal
- Patas pequeñas a los lados
- Ojos rojos brillantes
- Segmentos en el cuerpo
```

### Disfraz 2: Cucaracha Dañada
```
Igual a la normal pero con:
- Grietas o líneas de daño
- Color más oscuro
- Un ojo cerrado o dañado
- Usar cuando VidaJefe < 3
```

### Disfraz 3: Cucaracha Furiosa
```
Cucaracha con:
- Ojos más grandes y rojos
- Antenas más largas
- Color más intenso
- Usar cuando VidaJefe < 2
```

---

## 🎆 SPRITE 5: EXPLOSIÓN

### Disfraz 1: Explosión Pequeña
```
Forma: Círculo con rayos cortos
Color: Amarillo brillante
Tamaño: 20x20 pixels
Centro amarillo, rayos naranjas
```

### Disfraz 2: Explosión Mediana
```
Forma: Círculo más grande con rayos medianos
Color: Naranja con centro amarillo
Tamaño: 35x35 pixels
Más rayos que la anterior
```

### Disfraz 3: Explosión Grande
```
Forma: Estrella con muchos rayos
Color: Rojo con centro naranja
Tamaño: 50x50 pixels
Muchos rayos irregulares
```

---

## 💣 SPRITE 6: PROYECTIL DEL JEFE

### Disfraz Único: Proyectil Enemigo
```
Forma: Círculo pequeño o rombo
Color: Rojo brillante
Tamaño: 8x8 pixels
Efecto: Puede parpadear entre rojo y amarillo
```

---

## 🌌 FONDO: ESPACIO

### Fondo Principal
```
Color base: Negro (#000000)
Estrellas: Puntos blancos pequeños distribuidos aleatoriamente
Cantidad: 50-100 estrellas
Tamaños: Variados (1-3 pixels)
Opcional: Algunas estrellas de colores (azul, amarillo tenue)
```

---

## 📋 INSTRUCCIONES DE CREACIÓN EN SCRATCH

### Método 1: Dibujar en Scratch
1. Selecciona el sprite
2. Clic en la pestaña "Disfraces"
3. Clic en "Pintar"
4. Usa las herramientas:
   - **Círculo**: Para formas básicas
   - **Rectángulo**: Para la nave y proyectiles
   - **Pincel**: Para detalles
   - **Relleno**: Para colorear

### Método 2: Usar sprites de la biblioteca
1. Clic en "Elegir un Objeto"
2. Buscar sprites similares:
   - "Spaceship" para la nave
   - "Bat" para el murciélago
   - "Bug" o "Beetle" para la cucaracha
3. Modificar colores si es necesario

### Método 3: Importar desde archivo
1. Crear sprites en un programa externo (MS Paint, GIMP)
2. Guardar como PNG
3. En Scratch: "Subir Disfraz"
4. Seleccionar el archivo

---

## 🎨 TIPS DE DISEÑO

### Colores Recomendados
- **Nave**: Azul (#0080FF), Gris (#808080)
- **Proyectiles**: Amarillo (#FFFF00), Blanco (#FFFFFF)
- **Murciélago**: Negro (#000000), Morado (#800080)
- **Cucaracha**: Marrón (#8B4513), Negro (#000000)
- **Explosiones**: Amarillo (#FFFF00), Naranja (#FFA500), Rojo (#FF0000)

### Tamaños Relativos
- **Nave**: Tamaño base (100%)
- **Proyectil**: 25% del tamaño de la nave
- **Murciélago**: 75% del tamaño de la nave
- **Cucaracha**: 150% del tamaño de la nave
- **Explosión**: 50-200% (crece durante la animación)

### Animaciones
- **Murciélago**: Alternar disfraces cada 0.2 segundos
- **Cucaracha**: Cambiar disfraz según vida restante
- **Explosión**: Cambiar disfraces rápidamente (0.1 segundos)
- **Proyectiles**: Opcional rotación o parpadeo

---

## ✨ EFECTOS VISUALES ADICIONALES

### Para la Nave
```scratch
para siempre
├── cambiar efecto [brillo v] por [5]
├── esperar [0.1] segundos
├── cambiar efecto [brillo v] por [-5]
└── esperar [0.1] segundos
```

### Para Enemigos Dañados
```scratch
si <(vida) < [50]> entonces
├── fijar efecto [color v] a [25]
└── cambiar efecto [pixelar v] por [10]
```

### Para Proyectiles
```scratch
para siempre
├── cambiar efecto [color v] por [10]
└── esperar [0.05] segundos
```

¡Con estos diseños tendrás un Space Impact visualmente atractivo y fiel al original!