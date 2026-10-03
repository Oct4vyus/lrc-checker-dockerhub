
# 🎵 LRC Checker Herramienta Completa

Cuando te gusta reproducir música desde tu servidor leyendo las letras,  usas distintas aplicaciones como **Jellyfin**, **Navidrome** y **Symfonium**.
Entonces descubrís que no todo termina en configurar plugins que busquen y descarguen letras. Muchas no se encuentran, muchas estan desincronizadas 
y muchas son versiones de estudio que no coinciden con la versión en vivo.
Pero cuando tu biblioteca es grande, ¡cómo saber cuantos temas corresponden a cada grupo!.
Y si además accedes al servidor desde maquinas remotas con distintos sistemas operativos, ¡cuántas aplicaciones necesitás para averiguar y mejorar esto!.
Solo ésta y desde cualquier navegador web.  
Este contenedor Docker permite categorizar, crear, verificar y corregir letras sincronizadas en distintas bibliotecas musicales. 
Genera un informe visual (`lrc_report.html`) con categorías de clasificación y herramientas completas de edición de archivos `.lrc`.

---
## ✨ Como escanear tus carpetas musicales.
Hay varias formas:
- La primera vez que levantas el contenedor saludablemente, se genera un log. Ese es el primer escaneo y reporte.
- Puedes lanzarlo desde el navegador esccribendo  *http://IP-SERVIDOR:PUERTO/escanear*
- Desde el propio reporte con el botón **“Escanear ahora”**, ubicado al final del mismo

## ✨ Como categoriza

- Compara el **último timestamp** del archivo `.lrc` con la **duración real del audio**, según una **tolerancia configurable** en el docker-compose.yml.
```bash
# Diferencia en segundos a partir de la cual se considera error real (no posible final musical)
      - REVIEW_THRESHOLD_SEC=60.0
```
- Clasifica los resultados en 10 categorías dentro de `lrc_report.html`.
- Ocho categorias estan determinadas por el mecanismo de clasificación y dos por el usuario al actuar sobre los archivos.

Para una representación visual clara, las categorías se muestran primero en rectángulos, con el número correspondiente de los archivos clasificados en ellas.

Pero la parte útil e interactiva está justo arriba de la tabla de los archivos clasificados. Donde cada categoría es ahora un link que permite "filtrar", mostrando en la tabla solo una categoría concreta.


---

## 📊 Informe generado (`lrc_report.html`)

El informe organiza los archivos en las siguientes links a las categorías mostradas en la tabla:

- **Todos**     (Muestra la totalidad de archivos de audio escaneados )  
- ✅ OK         (El `.lrc` existe, es válido y estaría sincronizado correctamente o ya fue verificado.)    
- ❌ Missing    (Falta el archivo `.lrc` para ese audio.)
- ⚠️ Empty      (El archivo `.lrc` existe pero no tiene contenido útil.)
- ⚠️ Unsynced   (El `.lrc` no tiene marcas de tiempo.)
- ❌ Corrupt    (El archivo `.lrc` no se puede leer o tiene formato inválido.)
- ℹ️ Huérfanos   (Archivo `.lrc` sin audio relacionado.)

**Categorías definidas por probabilidades estadísticas**
- 🔴 Desync     (Diferencia moderada del último timestamp respecto a duración real del audio)
- 🟡 Revisar    (Hay una diferencia grande respecto a la duración real)

**Categorías definidas por acción del usuario**
- ✍️ Firmados  (Archivos ya revisados y corregidos, marcados con una `FIRMA` para dejarlos identificables      y posteriormente pasar a la categoría OK. La firma se define en el docker-compose.yml)
Ejemplo de configuración de la firma en `docker-compose.yml`:
```bash
environment:
  - SIGNED_MARKER=Oct4vyus Kandle
```
Sino se establece otra, Oct4vyus Kandle es la firma por defecto.


- 🎼 INSTRUMENTAL (Archivos marcados con "This song is an instrumental", para dejarlos identificables como sin letra.)



## ✨ Interactuar con la tabla de resultados
Podemos disparar distintas acciones desde las filas de la tabla.
- Las filas azules clasifican los archivos por álbums completos. Debajo de ellas se puede ver y editar cada archivo individual del álbum con el boton “EDITAR”.  
- Desde las filas azules se puede clasificar TODOS los archivos de un mismo álbum como ✍️ Firmados o 🎼 INSTRUMENTAL
- Desde las columnas AUDIO y .LRC de la tabla, haciendo click en sus archivos corresponientes, pueden descargarse esos archivos al disco local.  
  
- El **Botón “EDITAR”** en la fila de cada archivo individual, abre un EDITOR completo, para crear o reparar cualquier archivo    
  

---
## 🔧 Puesta en marcha 
Edita con tus variables el `docker-compose.yml`
```bash
services:
  lrc-checker:
    image: oct4vyus/lrc-checker:latest
    container_name: lrc-checker
    restart: unless-stopped

    # Variables de entorno configurables
    environment:
      # Define tu zona horaria
      - TZ=America/Argentina/Cordoba
      # Define las variables PUID/PGID. Pon el usuario / grupo que tendrá permisos para editar los LRC
      - PUID=0
      - PGID=0
      # Directorio donde está tu música (dentro del contenedor)
      - MUSIC_DIR=/music
      # Directorio donde se guardarán los reportes
      - OUTPUT_DIR=/reports
      # Diferencia a partir de la cual se considera error real (no posible final musical)
      - REVIEW_THRESHOLD_SEC=60.0
      # Verificar archivos .lrc que no tengan audio correspondiente
      - CHECK_ORPHANS=true
      # Mostrar progreso detallado en los logs
      - VERBOSE=true
      - SERVER_URL=http://CAMBIAR-POR-IP-DE-TU-SERVIDOR:8080
      # Lo que ingreses después del = sera la firma
      - SIGNED_MARKER=Oct4vyus Kandle
      # Opcional. Si no la ponés, se usa "Oct4vyus Kandle"

    # Volúmenes: monta tu biblioteca de música y directorio de reportes
    volumes:
      # CAMBIA ESTA RUTA por la ruta real de tu biblioteca de música
      - /ruta/a/tu/musica:/music:rw
      # Directorio donde se guardarán los reportes (JSON y HTML)
      - ./reports:/reports

    ports:
      - "8080:8080" # Cambiá el puerto si lo necesitás

    # Opcional: limitar recursos
    # deploy:
    #   resources:
    #     limits:
    #       cpus: '0.5'
    #       memory: 256M

```

## ⚡ Quick Start

1. Configurar en `docker-compose.yml` las variables básicas:  
   - Ruta de tu biblioteca de música.  
   - Carpeta donde se generan los informes.  
   - Puerto e IP del servidor.  
2. Levantar el contenedor. 

```bash


# Levantar contenedor
docker-compose up -d


```
Luego abrir en el navegador:

3. Verificar conexión:  

http://IP-SERVIDOR:PUERTO/status
Debe responder **OK**. 
 
4. Generar primer informe:  

http://IP-SERVIDOR:PUERTO/escanear

En bibliotecas grandes puede tardar. Al finalizar muestra:  
*Escaneo completo: XX archivo(s) en estado REVISAR, XX OK de XX total.*  
5. Abrir el archivo `lrc_report.html` en la carpeta de informes para revisar y reparar.  

---


🤝 Contribuciones
¡Las contribuciones son bienvenidas! Abre un issue o un pull request para sugerir mejoras.

📜 Licencia
MIT License

## Captura de pantalla del informe

El siguiente ejemplo muestra un reporte generado por **LRC Checker v2.0.0**, donde se resumen los resultados del escaneo de la biblioteca:

![Captura de pantalla del informe](https://github.com/Oct4vyus/lrc-checker-jellyfin-navidrome/raw/main/docs/imagenes/lrc-checker-report.png)