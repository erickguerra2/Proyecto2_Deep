# Bestiario de mazmorra: criaturas nuevas con una GAN

Proyecto 2 · Diseño generativo de personajes · Deep Learning 2026 · Universidad del Valle

**Integrantes:** Erick Guerra · Carlos Alburez

Una DCGAN entrenada con sprites de monstruos de pixel art para generar criaturas de fantasía que no existen en los datos de entrenamiento.

## Contenido del repo

- `Proyecto2_GAN.ipynb`: notebook principal. Cubre el universo, los datos, el modelo, las corridas, la galería y la comprobación de novedad.
- `galeria/`: 10 criaturas seleccionadas en PNG, con su semilla. *(pendiente)*
- `salidas/`: pesos y rejillas de entrenamiento. No se suben (ver `.gitignore`).
- `data/`: descarga de DCSS. Se genera al ejecutar el notebook y no se sube.

## Cómo ejecutar

**En Google Colab (recomendado):** *Entorno de ejecución → Cambiar tipo de entorno → GPU*, luego *Ejecutar todo*.

**En local:**
```
pip install torch torchvision matplotlib pillow numpy
```
Luego abre `Proyecto2_GAN.ipynb` y ejecuta todas las celdas en orden. La primera vez el notebook descarga el repo de DCSS (unos 37 MB) en `data/`.

El dispositivo se elige solo: CUDA si hay GPU NVIDIA, MPS en Mac con chip Apple, y CPU si no hay ninguna de las dos (más lento).


## Semillas y reproducibilidad

- Semilla global: `0`.
- Las tres corridas (base, hinge y normalización espectral) usan la misma configuración: `CONFIG` en la sección 0.
- Rejilla fija: 16 vectores de ruido generados con la semilla 0. Son los mismos en todas las corridas.
- Galería: 200 criaturas con las semillas 0 a 199. Se seleccionan 10
- Los resultados pueden variar un poco entre CUDA, MPS y CPU por diferencias de punto flotante.

## Datos y atribución

Los datos son sprites de monstruos de **Dungeon Crawl Stone Soup** (DCSS), carpeta `crawl-ref/source/rltiles/mon`, en el commit fijo `034f1973d3d05beef63cc8987ddb7e7f490eea2c`.

- Fuente: https://github.com/crawl/crawl
- Licencia: el repositorio declara que la mayoría de sus tiles son CC0.
- Carpetas usadas: aberrations, abyss, amorphous, animals, aquatic, demihumanoids, demons, draco, dragons, eyes, fungi_plants, holy, humanoids, mutantbeast, nonliving, panlord, statues, tentacles, undead.
- Excluidas: `player`, `sprint`, `unique` y `vault`, porque son personajes con nombre propio o del jugador.
- Se usan 1 335 imágenes únicas de 32 × 32, ampliadas a 64 × 64 sin suavizar.
- Aumento de datos: volteo horizontal y desplazamiento de hasta 2 píxeles.

Los sprites de DCSS se tratan como material de estilo. La galería se compara con ellos para comprobar que no los copia.

## Declaración de uso de IA

Se usó Claude (Anthropic) como ayuda en dos cosas:
- **Estructura del esqueleto del notebook:** orden de las secciones, limpieza de las instrucciones de la plantilla y revisión de la organización.
- **README:** redacción del borrador de este archivo y revisión de la redacción.

El código, los experimentos, las decisiones de diseño, la interpretación de resultados y la selección de criaturas son responsabilidad de los integrantes.