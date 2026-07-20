# GeoSalud Tongoy

Visor territorial de apoyo a rescate y continuidad de cuidado — Red CESFAM
Tongoy / Posta de Salud Rural Guanaqueros, Departamento de Salud Coquimbo.

## ⚠️ Antes de nada: política de datos

**Este repositorio no contiene, y nunca debe contener, datos de pacientes.**
Nombres, RUT, direcciones y teléfonos son datos sensibles de salud
(Ley 19.628). El `.gitignore` ya excluye `datos/`, `salida/` y cualquier
`.csv`, pero revisa siempre `git status` antes de hacer `git push` — un
`.gitignore` no deshace un commit que ya subiste por error.

El visor web (`app/visor.html`) está diseñado para esto: **no trae datos
incluidos**. Cada persona del equipo carga su copia del CSV normalizado
directamente en el navegador, al abrir la página. Ese archivo se procesa
enteramente en el computador de quien lo abre — nunca se sube a GitHub, a
ningún servidor, ni siquiera a la conexión a internet (todo el procesamiento
ocurre en JavaScript, dentro de la pestaña del navegador).

La base de datos (`usuarios_normalizado.csv`) se comparte por el canal que
ya use tu equipo para fichas clínicas — disco compartido institucional,
o similar — nunca por GitHub, correo personal ni WhatsApp.

## Qué hay en este repo

```
geosalud-tongoy/
├── app/
│   ├── visor.html          el visor — se abre en el navegador
│   └── sectores_tongoy.js  los 3 polígonos de sectorización (sin datos de personas)
├── scripts/                pipeline de normalización y geocodificación
└── README.md
```

## Cómo usarlo (para el equipo)

1. Clona o descarga este repositorio.
2. Pide el `usuarios_normalizado.csv` más reciente al encargado de biostadística.
3. Abre `app/visor.html` con doble clic.
4. En la pantalla de carga, selecciona ese CSV.
5. Listo — mapa, filtros y tabla quedan operativos con los datos vigentes.

Si además ya corriste la geocodificación (ver más abajo), usa el botón
**"Cargar CSV geocodificado"** dentro del visor para incorporar coordenadas
exactas sobre la base ya cargada.

## Publicarlo con GitHub Pages (opcional, recomendado)

Como `app/` no contiene datos, es seguro publicarlo como sitio estático:
GitHub Pages solo sirve los archivos del repo — nadie sube datos por
publicar la página, cada usuario sigue cargando su CSV localmente.

```
Settings → Pages → Source: rama main, carpeta /app → Save
```

El equipo accede desde una URL fija en vez de tener que descargar el HTML
cada vez. Aun así, sigue siendo obligatorio que cada persona tenga permiso
para ver los datos de pacientes que va a cargar.

## Pipeline completo (biostadística / mantención)

```bash
pip install pandas geopy shapely --break-system-packages

python scripts/01_normalizar.py            # limpia la base cruda -> salida/usuarios_normalizado.csv
python scripts/02_kml_a_geojson.py         # KML de sectorización -> GeoJSON
python scripts/03_geocodificar_y_cruzar.py # geocodifica (requiere internet, corre horas)
python scripts/04_generar_datos_app.py     # solo si usas la variante de datos embebidos, ver nota abajo
node scripts/05_generar_informe.js         # regenera el informe ejecutivo .docx
```

Los insumos (`datos/GEOLOCALIZACION_...csv`, `datos/SECTORIZACION_...kml`)
y las salidas (`salida/`) no van en este repo — corren en local o en un
servidor institucional con acceso restringido.

### Nota sobre `04_generar_datos_app.py`

Ese script genera `app/datos_usuarios.js` con los datos **embebidos** en el
archivo — es la variante para uso 100% local en un PC del CESFAM sin
depender de que alguien recuerde cargar el CSV cada vez. Si lo generas,
**no lo subas a git** (ya está en `.gitignore`): es exactamente el archivo
con datos reales de pacientes que este repositorio público no debe tener.
Úsalo solo para copias locales fuera de control de versiones.

## Resguardo

Acceso al repo: mantenlo **privado** en GitHub (Settings → General →
Danger Zone → Change visibility) aunque no tenga datos, para no exponer
públicamente la lógica de priorización sanitaria ni la estructura interna
de la red asistencial. Consulta con tu encargado de protección de datos
antes de ampliar el acceso.
