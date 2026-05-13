# Metodo de Web Scraping - Biblioteca Digital UN y Labordoc OIT

Herramienta de linea de comandos para buscar, descargar y procesar
documentos PDF desde la Biblioteca Digital de las Naciones Unidas y el
repositorio Labordoc de la Organizacion Internacional del Trabajo.

El programa extrae los metadatos, descarga los archivos PDF, lee su
contenido textual y mantiene un historial acumulado entre sesiones para
evitar descargar el mismo documento dos veces.


## Estructura del proyecto

```
proyecto_scraper/
    main.py              
    base_scraper.py      
    scraper_un.py        
    scraper_ilo.py       
    configuracion.json   
    requirements.txt     
    README.md            
```


## Requisitos previos

- Python 3.9 o superior
- Conexion a internet
- Windows 10/11, macOS o Linux


## Instrucciones de instalacion

### Paso 1: Instalar Python

**Windows:**
1. Ve a https://www.python.org/downloads/
2. Descarga la version mas reciente (boton amarillo grande).
3. Ejecuta el instalador.
4. IMPORTANTE: Marca la casilla "Add Python to PATH" antes de hacer clic
   en "Install Now".
5. Espera a que termine la instalacion y cierra el instalador.

**macOS:**
1. Abre la Terminal (busca "Terminal" en Spotlight).
2. Escribe: `brew install python3`
   Si no tienes Homebrew, instala Python desde https://www.python.org/downloads/

**Linux (Ubuntu/Debian):**
1. Abre una terminal.
2. Escribe: `sudo apt update && sudo apt install python3 python3-pip`

### Paso 2: Instalar Visual Studio Code

1. Ve a https://code.visualstudio.com/
2. Descarga la version para tu sistema operativo.
3. Instala VS Code con las opciones por defecto.
4. Abre VS Code.
5. (Opcional) Instala la extension "Python" de Microsoft:
   - Haz clic en el icono de extensiones (barra lateral izquierda).
   - Busca "Python" y haz clic en "Install" en la primera opcion.

### Paso 3: Abrir el proyecto en VS Code

1. Descarga o copia la carpeta a tu computadora.
2. En VS Code, ve a Archivo > Abrir carpeta (File > Open Folder).
3. Selecciona la carpeta.
4. VS Code abrira el proyecto y veras los archivos en el panel izquierdo.

### Paso 4: Abrir la terminal integrada

1. En VS Code, ve a Terminal > Nueva terminal (Terminal > New Terminal).
   Tambien puedes usar el atajo: Ctrl + ` (la tecla al lado del 1).
2. Se abrira una terminal en la parte inferior de la pantalla.
3. Verifica que la terminal este en la carpeta del proyecto.
   Deberia mostrar algo como: `C:\Users\TuNombre\proyecto_scraper>`

### Paso 5: Instalar dependencias

Escribe el siguiente comando en la terminal de VS Code y presiona Enter:

```
pip install -r requirements.txt
```

Esto instalara: requests, playwright, lxml, pandas y pypdf.

Luego, instala el navegador que Playwright necesita:

```
playwright install chromium
```

Esto descargara el navegador Chromium. Es necesario porque ambas fuentes
requieren un navegador con motor JavaScript real para acceder a sus
resultados de busqueda (Labordoc por su interfaz Angular, y la UN
Digital Library por su mecanismo de proteccion contra clientes HTTP
planos).

### Paso 6: Verificar la instalacion

Escribe en la terminal:

```
python main.py
```

Selecciona la opcion **[2] Diagnostico de dependencias** para verificar
que todo este correctamente instalado.

**Si aparece "python no se reconoce como comando":**
- En Windows, intenta con `py main.py` en lugar de `python main.py`.
- Si sigue sin funcionar, reinstala Python asegurandote de marcar
  "Add Python to PATH".


## Como usar el programa

### Ejecucion

Desde la terminal de VS Code:

```
python main.py
```

### Flujo del menu

1. **Menu principal:** Selecciona la opcion [1] "Buscar y descargar
   documentos".

2. **Seleccionar fuente:** Elige entre UN Digital Library o ILO Labordoc.

3. **Configurar filtros:**
   - **Palabra clave** (obligatorio, maximo una palabra o termino corto).
     Ejemplo: `wellbeing`
   - **Rango de fechas:** fecha desde y fecha hasta (opcional).
   - **Idioma:** codigo de dos letras (en, es, fr, ar, zh, ru) o dejar
     vacio. Se pueden combinar hasta dos idiomas separados por coma.
   - **Tipo de documento** (opcional):
     - En UN Digital Library: reporte, resolucion, acuerdo, decision,
       carta.
     - En ILO Labordoc: reporte, resolucion, acuerdo, libro, articulo.
   - **Limite de documentos:** por defecto 100, que es tambien el maximo
     permitido por sesion.

4. **Seleccion de carpeta:** se abre un selector grafico para elegir el
   directorio de destino. Si el sistema no soporta interfaz grafica, se
   puede escribir la ruta directamente por teclado.

5. **Confirmar:** revisa el resumen de la configuracion y confirma con
   "s".

6. **Resultados:** al finalizar, el programa muestra un resumen con
   documentos exitosos, fallidos, tiempos de procesamiento y rutas de
   los archivos generados.

### Ejemplo de sesion

```
  Palabra clave (Max 1 palabra): wellbeing
  Fecha desde (ej: 2015): 2020
  Fecha hasta (ej: 2024): 2024
  Codigo de idioma (ej: es): en
  Tipo de documento: reporte
  Numero de documentos a descargar (Max 100, default: 100): 15
```


## Archivos generados

Cada sesion genera los siguientes archivos en la carpeta seleccionada:

| Archivo | Descripcion |
|---------|-------------|
| `*.pdf` | Los archivos PDF descargados, con nombres estandarizados |
| `metadata.csv` | Metadatos en formato CSV (tabular, para Excel/Sheets) |
| `metadata.json` | Metadatos en formato JSON (estructurado, con texto completo) |
| `textos_extraidos.txt` | Texto consolidado de todos los PDFs descargados |

Adicionalmente, en la raiz del proyecto:

| Archivo | Descripcion |
|---------|-------------|
| `historial_descargas.json` | Indice acumulado entre sesiones de todos los documentos procesados |
| `errores.log` | Registro detallado de la sesion actual (se sobrescribe cada sesion) |

### Formato de los metadatos (CSV y JSON)

Ambos archivos contienen los mismos campos para cada documento:
- `titulo`
- `autor`
- `fecha` — fecha de publicacion del documento
- `idioma`
- `tipo_documento`
- `url_fuente`
- `archivo_local` — nombre del PDF o "DESCARGA_FALLIDA"
- `fecha_descarga` — momento exacto de la descarga, formato ISO 8601
  (ej: `2026-04-21T15:42:31`)
- `texto_extraido` — contenido textual del PDF

### Archivo de textos consolidado (`textos_extraidos.txt`)

Contiene el texto extraido de todos los PDFs en un solo archivo,
separados por encabezados claros que indican el archivo, titulo, fecha
de publicacion y fecha de descarga de cada documento:

```
======================================================================
DOCUMENTO 1 de 15
ARCHIVO:           ILO_alma995339593202676_Issue_paper.pdf
TITULO:            Issue paper on child labour and climate change
FECHA PUBLICACION: 2023
FECHA DESCARGA:    2026-04-21T15:42:31
======================================================================

[texto del PDF aqui]

```

Este archivo se sobrescribe en cada sesion y facilita la revision manual
del contenido sin necesidad de abrir cada PDF individualmente.

### PDFs sin texto extraible (escaneados)

Algunos PDFs son escaneos de documentos en papel: contienen imagenes en
lugar de texto digital. Estos no se pueden leer con extraccion simple.

Cuando el programa detecta un PDF asi, el campo `texto_extraido`
muestra:
`[PDF SIN CAPA DE TEXTO - OCR REQUERIDO]`

El PDF queda descargado correctamente, simplemente no se le puede sacar
texto sin un proceso adicional de OCR (reconocimiento optico de
caracteres). Esto podria agregarse en una version futura del programa
si fuera necesario.

Otros marcadores que pueden aparecer en `texto_extraido`:
- `[PDF VACIO O SIN CONTENIDO TEXTUAL]` — el PDF no tenia paginas con
  texto
- `[ERROR AL LEER EL PDF]` — el archivo estaba corrupto o protegido
- `[ARCHIVO NO DESCARGADO]` — la descarga del PDF fallo


## Historial acumulado de descargas

El archivo `historial_descargas.json` (en la raiz del proyecto) guarda
un indice de todos los documentos que el programa ha intentado
descargar, sean exitosos o fallidos. Esto evita que:

- Descargues dos veces el mismo documento aunque corras busquedas
  repetidas.
- Reintentes documentos que ya fallaron anteriormente (algunos
  documentos simplemente no tienen PDF disponible y seguirian fallando).

### Como funciona

Al iniciar una busqueda, el programa lee el historial y le pasa al
scraper la lista de identificadores ya conocidos. El scraper los salta
silenciosamente durante la paginacion y sigue buscando hasta completar
el numero de documentos que pediste.

Cada documento queda identificado en el historial mediante una clave
compuesta que combina el prefijo de la fuente y el identificador
interno (por ejemplo, `UN:4012345` o `ILO:alma995339593202676`),
evitando colisiones entre las numeraciones propias de cada repositorio.

Ejemplo: si pides 15 documentos sobre "climate change" y el programa
detecta que 12 ya estan en tu historial, buscara en mas paginas hasta
conseguir 15 documentos nuevos reales (o hasta que no queden mas en la
fuente).

Al final de la sesion se muestra cuantos documentos hay en total en el
historial acumulado.

### Como borrar o editar el historial

- Para **borrar todo el historial** (empezar de cero): borra el archivo
  `historial_descargas.json`. El programa lo crea de nuevo la siguiente
  sesion.
- Para **borrar entradas especificas** (forzar re-descarga de ciertos
  documentos): abre el archivo con el Bloc de notas, busca la entrada
  correspondiente por su ID o titulo, y borra ese bloque. Cuida de no
  romper la estructura JSON (comas, llaves).
- Para **inspeccionar que tienes descargado**: el archivo es legible en
  cualquier editor de texto. Cada entrada incluye titulo, fuente, fecha
  de publicacion, fecha de descarga, ruta del archivo y estado
  (exitoso o fallido).

Si el archivo se corrompe por error, el programa muestra un mensaje
claro al iniciar y sigue funcionando sin la deteccion de duplicados
hasta que lo arregles o lo borres.


## Solucion de problemas

### "No se encontraron documentos"
- Verifica que las palabras clave sean correctas.
- Amplia el rango de fechas o elimina filtros restrictivos.
- Prueba primero sin filtros de idioma o tipo de documento.
- El programa intenta automaticamente relajar el filtro de tipo de
  documento si la busqueda inicial no produce resultados, e informa de
  ello al usuario.

### "Error de conexion" o "Timeout"
- Verifica tu conexion a internet.
- Los servidores pueden estar temporalmente lentos. El programa
  reintenta hasta tres veces con esperas crecientes (3, 8 y 15
  segundos) antes de declarar un fallo definitivo.
- Si usas VPN, desactivala temporalmente.

### "Playwright no esta instalado"
- Ejecuta: `pip install playwright`
- Luego: `playwright install chromium`

### "Descarga fallida" en muchos documentos
- Algunos documentos no tienen PDF disponible (solo metadatos).
- Revisa `errores.log` para ver el detalle de cada error.
- Los enlaces pueden requerir acceso institucional.

### Compartir errores para obtener ayuda
Si el programa falla, comparte el archivo `errores.log`. Contiene
marcas de tiempo, URLs que fallaron, tipo de error y la traza completa.
Cada evento esta separado con lineas "---" para facilitar la lectura.

Ten en cuenta que `errores.log` se sobrescribe al inicio de cada
sesion, por lo que conviene compartirlo inmediatamente despues de que
ocurra el problema.

## Agregar nuevas fuentes

Consulta las instrucciones detalladas al final de `main.py` en la
seccion "COMO AGREGAR UNA NUEVA FUENTE". En resumen:

1. Crea un archivo `scraper_nueva_fuente.py`
2. Hereda de `BaseScraper` e implementa `nombre_fuente()`, `search()`
   y `download()`
3. Registra el scraper en `obtener_scrapers_disponibles()` dentro de
   `main.py`
4. Prueba con una busqueda pequena
5. Documenta la estrategia y dependencias


## Archivo de configuracion (configuracion.json)

El archivo `configuracion.json` se crea automaticamente la primera vez
que ejecutas el programa. Contiene parametros que puedes cambiar sin
tocar codigo Python:

- `fecha_minima_permitida`: fecha mas antigua que el programa acepta
  (default: 1945).
- `fecha_maxima_permitida`: fecha mas reciente que el programa acepta
  (default: 2026).
- `carpeta_descarga_por_defecto`: carpeta donde se guardan los PDFs.
- `limite_documentos_por_defecto`: cuantos documentos buscar como
  maximo por sesion (default: 100).
- `ultima_carpeta_usada`: se actualiza automaticamente con la ultima
  carpeta elegida en el selector grafico.
- `ilo_search_scope`: scope de busqueda de ILO Labordoc
  (default: ALL_ILO).
- `ilo_tab`: tab de busqueda de ILO Labordoc (default: ALL_ILO).
- `idiomas_validos`: codigos de idioma reconocidos y sus nombres en
  espanol.

Si el archivo se corrompe (por ejemplo, si se borra una coma o una
comilla), el programa mostrara un mensaje de error claro indicando que
linea tiene el problema, y seguira funcionando con valores por defecto.


## Como ampliar el rango de fechas

El programa valida que las fechas ingresadas esten dentro de un rango
permitido. Por defecto, la fecha maxima es 2026. Si necesitas buscar
documentos de 2027 en adelante, sigue estos pasos:

1. Abre el archivo `configuracion.json` con el Bloc de notas (Windows)
   o cualquier editor de texto.

2. Busca la linea que dice:
       "fecha_maxima_permitida": 2026,

3. Cambia el numero 2026 por la fecha que necesites, por ejemplo:
       "fecha_maxima_permitida": 2027,

4. Guarda el archivo y cierra el Bloc de notas. La proxima vez que
   ejecutes el programa, aceptara fechas hasta 2027.

IMPORTANTE: no borres las comillas, las comas ni las llaves del
archivo. Si el programa muestra un error al iniciar, revisa que el
formato sea correcto o borra el archivo `configuracion.json` para que
se regenere con los valores por defecto.
