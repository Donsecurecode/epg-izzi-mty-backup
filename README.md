# EPG izzi Monterrey (XMLTV) — repositorio de RESPALDO

Genera automáticamente una guía de programación (EPG en formato **XMLTV**) de
**izzi TV — región Monterrey**, lista para cargarse en **Xtream UI** u otro panel
/ reproductor IPTV.

> ⚠️ **Este es el repositorio de respaldo.** El principal es
> [`Sayonararoku/epg-izzi-mty`](https://github.com/Sayonararoku/epg-izzi-mty).
> Ambos generan exactamente el mismo EPG de forma independiente, con los cron
> **desfasados 12 horas**, para que si uno falla el otro siga publicando fresco.

| Repositorio | Hora del cron (UTC) | Hora centro MX |
|-------------|---------------------|----------------|
| Principal   | 05:00               | 23:00          |
| **Respaldo (este)** | **17:00**   | **11:00**      |

## Salida

El workflow publica `izzimty.xml.gz` (XMLTV comprimido) en la rama **`epg`**,
que se reescribe entera en cada corrida: siempre 1 commit y 1 archivo, así el
repositorio no crece aunque el EPG se publique todos los días.

URL pública:

```
https://raw.githubusercontent.com/Donsecurecode/epg-izzi-mty-backup/epg/izzimty.xml.gz
```

> La rama `main` solo guarda el código. No busques el EPG ahí.

## Cómo cargarlo en Xtream UI

1. En el panel ve a **EPG → Add EPG**.
2. Nombre: `izzi Monterrey (respaldo)`.
3. **EPG URL:** la URL de arriba.
4. Guarda y deja que Xtream UI lo descargue con su propio cron.
5. En cada canal/stream, pon el **EPG ID** con el formato **`IzzI.<serviceId>`**
   (p. ej. `IzzI.45399` para NFL NETWORK HD).

> El `epg_channel_id` debe coincidir **exactamente** con el `id` del `<channel>`
> del XML, o Xtream UI no asociará la programación.

## Configuración (variables de entorno, opcionales)

| Variable          | Default      | Descripción                              |
|-------------------|--------------|------------------------------------------|
| `EPG_OUTPUT_DIR`  | `.`          | Carpeta de salida.                       |
| `EPG_FILENAME`    | `izzimty.xml`| Nombre del archivo XML.                  |
| `EPG_DAYS`        | `15`         | Días de programación a futuro (máx. ~18).|
| `EPG_REGION`      | `Monterrey`  | Región de izzi.                          |
| `EPG_MAX_WORKERS` | `20`         | Descargas en paralelo.                   |
| `EPG_VERIFY_TLS`  | `false`      | Verificar certificados TLS.              |

### Regiones disponibles

La API acepta, entre otras: `Monterrey`, `Tijuana`, `Saltillo`, `Reynosa`,
`Nuevo Laredo`, `Matamoros`, `Ensenada`, `Mexicali`, `Chihuahua`, `Pachuca`,
`Tampico`, `San Luis Potosi`, `Villahermosa`, `Merida`, `Irapuato`, `Cancun`,
`Campeche`, `Coatzacoalcos`, `Celaya`, `Aguascalientes`, `Cuernavaca`,
`Zacatecas`, `Poza Rica`, `Queretaro`, `Oaxaca`, `Chetumal`, `Cordoba`,
`Orizaba`, `Acapulco`.

Todas comparten la misma parrilla nacional; solo cambian los canales locales
(p. ej. Tijuana añade las señales abiertas de San Diego).

## Ejecutar localmente

```bash
pip install -r requirements.txt
python epg_monterrey.py
```

Genera `izzimty.xml` y `izzimty.xml.gz` en la carpeta actual.

## Notas

- El script es tolerante a fallos: reintenta automáticamente, omite canales que
  fallen y **no** escribe un EPG vacío (si la API falla por completo, el job
  termina en error y conserva el último EPG válido ya publicado).
- GitHub puede desactivar el cron tras ~60 días de inactividad del repositorio;
  en ese caso llega un correo con un botón para reactivarlo.
