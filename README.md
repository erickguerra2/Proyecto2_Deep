# Bestiario de mazmorra: criaturas nuevas con una GAN

Proyecto 2 · Diseño generativo de personajes · Deep Learning 2026 · Universidad del Valle

**Integrantes:** Erick Guerra · Carlos Alburez

Una DCGAN entrenada con sprites de monstruos de pixel art para generar criaturas de fantasía que no existen en los datos de entrenamiento.

## Contenido del repo

- `Proyecto2_GAN.ipynb`: notebook principal. Cubre el universo, los datos, el modelo, las corridas, los experimentos de pérdida y de estabilización, la galería, la comprobación de novedad y la regeneración.
- `galeria/`: 10 criaturas seleccionadas en PNG (64×64, y 256×256 en `ampliadas/`), junto con `z_vectores.npy` y `semillas.json` (semilla y vector z de cada una).
- `pesos/`: pesos del generador del modelo final (`G_final.pt`).
- `salidas/`: pesos y rejillas de entrenamiento de las tres corridas. No se suben (ver `.gitignore`).
- `data/`: descarga de DCSS. Se genera al ejecutar el notebook y no se sube.

## Cómo ejecutar

**En Google Colab (recomendado):** _Entorno de ejecución → Cambiar tipo de entorno → GPU_, luego _Ejecutar todo_. Con GPU de Colab cada corrida de 500 épocas tarda unos minutos.

**En local:**

```
pip install torch torchvision matplotlib pillow numpy
```

Luego abre `Proyecto2_GAN.ipynb` y ejecuta todas las celdas en orden. La primera vez el notebook descarga el repo de DCSS (unos 37 MB) en `data/` y los pesos de ResNet-18 (se necesita internet). En una Mac con MPS, cada corrida de 500 épocas tarda entre 30 y 40 minutos.

El dispositivo se elige solo: CUDA si hay GPU NVIDIA, MPS en Mac con chip Apple, y CPU si no hay ninguna de las dos (más lento).

**Versiones usadas:** _(completar con la salida de este comando)_

```
python -c "import sys, torch, torchvision, numpy, matplotlib, PIL; print(sys.version.split()[0], torch.__version__, torchvision.__version__, numpy.__version__, matplotlib.__version__, PIL.__version__)"
```

## Semillas y reproducibilidad

- Semilla global: `0`.
- Las tres corridas (base, hinge y normalización espectral) usan la misma configuración: `CONFIG` en la sección 0. Solo cambia lo que dice la hipótesis de cada experimento.
- Rejilla fija: 16 vectores de ruido generados con la semilla 0. Son los mismos en todas las corridas.
- Modelo final: **hinge** (`MODELO_FINAL = "hinge"` en la sección 8).
- Galería: 200 criaturas con las semillas 0 a 199. Se escogieron 10 a ojo (tasa de selección 10 de 200, 5 %), por silueta legible y variedad de forma y color, descartando las vacías o de solo trazos. Semillas elegidas: `[0, 3, 10, 24, 36, 42, 58, 113, 174, 194]`. Después se comprobó su novedad con los vecinos más cercanos (ResNet-18 y píxeles).
- Cada criatura sale solo de su semilla: el vector z se genera con un generador de PyTorch en CPU y la imagen se crea en CPU, una por una, así que el resultado no depende de la GPU.
- Los resultados de entrenamiento reportados se obtuvieron en una Mac con MPS. Pueden variar un poco en CUDA o CPU por diferencias de punto flotante.
- Regeneración: la sección 11 del notebook reproduce las 10 imágenes a partir de `pesos/G_final.pt` y las semillas declaradas, sin entrenar nada (diferencia de 0 en todos los píxeles).

## Resultados resumidos

| Corrida                 | Épocas con D<0.1 (de 500) | Diversidad (500 ruidos, ResNet-18) |
| ----------------------- | :-----------------------: | :--------------------------------: |
| base (BCE no saturante) |            324            |               0.186                |
| hinge                   |            250            |               0.181                |
| normalización espectral |            359            |               0.186                |

Hinge fue algo más estable y se usó como modelo final, con una ventaja pequeña. La normalización espectral no ayudó. Las criaturas tienen manchas de color y texturas tipo ruido (ver limitaciones en el notebook).

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

Se usó Claude (Anthropic) como ayuda en lo siguiente:

- **Estructura del esqueleto del notebook:** orden de las secciones, limpieza de las instrucciones de la plantilla y revisión de la organización.
- **README:** redacción del borrador de este archivo y revisión de la redacción.
