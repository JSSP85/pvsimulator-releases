# Librería 3D compartida

Esta carpeta es la **librería 3D online** de TrackMesh Studio. La app la lee por
internet desde:

```
https://raw.githubusercontent.com/JSSP85/pvsimulator-releases/main/library/
```

Al abrir la **Galleria elementi 3D** (sección Progettazione), la app descarga
`catalog.json` y, bajo demanda, los `.glb` declarados en él. **Subir un modelo
aquí lo hace aparecer al instante en la galería de todos los usuarios, sin
publicar una nueva versión de la app.** Cada `.glb` descargado se cachea en
local, así que también funciona offline tras la primera vez.

## Cómo añadir un modelo

1. Sube el archivo `.glb` a esta misma carpeta `library/`.
2. Añade una entrada en `catalog.json` dentro del array `items`.
3. Haz commit a `main`. Listo — no hace falta release.

## Formato de `catalog.json`

```json
{
  "items": [
    { "file": "trattore-fendt.glb", "name": "Trattore Fendt", "type": "tractor", "scale": 1 },
    { "file": "lampione.glb",       "name": "Lampione",       "type": "light",   "scale": 1 },
    { "file": "vite.glb",           "name": "Vite AgriPV",    "type": "plant",   "scale": 1.2 }
  ]
}
```

El catálogo arranca vacío:

```json
{ "items": [] }
```

### Campos de cada item

| Campo   | Obligatorio | Descripción |
|---------|-------------|-------------|
| `file`  | sí          | Nombre del `.glb` en esta misma carpeta `library/`. |
| `name`  | sí          | Nombre visible en la galería. |
| `type`  | sí          | Comportamiento al colocar el modelo (ver tabla abajo). |
| `scale` | no          | Multiplicador fino sobre el auto-ajuste de tamaño. `1` = auto (por defecto). |

### Valores de `type`

| `type`    | Comportamiento |
|-----------|----------------|
| `object`  | Objeto decorativo genérico (el habitual). Se coloca tal cual, normalizado al suelo. |
| `light`   | Como `object` pero **añade una lámpara nocturna** (se enciende en la vista nocturna). |
| `plant`   | **Crece bajo las filas AgriPV**: se instancia repetido en el terreno entre trackers. |
| `tractor` | Vehículo agrícola (escala/altura creíbles de tractor). |
| `cabin`   | Cabina / caseta de MT. |
| `person`  | Figura humana (referencia de escala). |
| `post`    | Parte del tracker: **poste** (soporte hincado). |
| `moving`  | Parte del tracker: **parte móvil** (eje/torque tube que rota). |
| `panel`   | Parte del tracker: **panel** fotovoltaico. |
| `tracker` | **Assieme completo del tracker** (postes + tubo de torsión + rodamientos + rieles… en un solo `.glb` exportado de CAD). Aparece como opción elegible en **Officina tracker → Assieme completo**, junto a "Importa file…" — no en la galería de Oggetti 3D (esa es para props decorativos, ver `Risorse 3D` en las Releases). |

Los modelos se normalizan solos (tamaño real creíble por tipo, base apoyada en
el suelo), así que cualquier `.glb` razonable de Sketchfab/Poly funciona.
`scale` sólo hace falta para retocar casos concretos.

## Vegetación del render fotográfico

Dos archivos de esta carpeta **no** están en `catalog.json` y no aparecen en la
galería, a propósito: los consume el **Render fotográfico** de la app (pestaña
Home → Immagine) y no tiene sentido colocarlos a mano, uno por uno, como si
fueran props.

| Archivo | Qué es | Cómo lo usa el render |
|---------|--------|-----------------------|
| `realtime_grass.glb` | Parche de césped en tarjetas alfa (~37 triángulos/m²) | Se esparce con solape y giro aleatorio alrededor del punto que mira la cámara, con la altura reducida a un tercio (césped segado bajo las filas) |
| `daisy_models_pack_realistic_optimized.glb` | 27 variantes de planta con flor en un solo archivo | Se separa por malla y se siembra en una franja bajo cada fila de trackers, **encima** del césped |

La app los busca **por nombre**, así que renombrarlos los desactiva. Para probar
un reemplazo sin tocar el repo, en la consola del navegador:

```js
localStorage.setItem('tm.veg.grass',   'mi-cesped.glb');
localStorage.setItem('tm.veg.flowers', 'mis-flores.glb');
```

`simple_grass_chunks.glb` está aquí pero **no se usa**: se exportó sin las
texturas de color base (sólo normales y rugosidad), así que se renderiza blanco,
y a ~7500 triángulos/m² es unas doscientas veces más caro que el otro. Sirve si
algún día se re-exporta con sus texturas.
