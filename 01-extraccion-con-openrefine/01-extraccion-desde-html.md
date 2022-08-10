<html><!--
 Stuff to add that was not in chapter
 - that only one class (poem) is not enough and then we need to make the search sensitive to verse as well
 - can point to errors in the storni extraction and say how that should be solved
 --></html>

# Extracción de contenido a partir de fuentes HTML con OpenRefine

La extracción de contenido o *scraping* con OpenRefine, partiendo de fuentes HTML (o de los URLs que llevan a ellas) se ha descrito en las secciones 4.1 y 4.2 del capítulo.


El marcado HTML y sus características relevantes para la extracción se presentaron en la sección 2.2. del capítulo.

Comenzamos aquí directamente con las manipulaciones a efectuar en OpenRefine.

## Instalación

OpenRefine se puede descargar desde https://openrefine.org/download.html

Las instrucciones de instalación están en https://docs.openrefine.org/manual/installing

OpenRefine es multiplataforma (Windows, Linux, Mac). Requiere el entorno Java, pero si no está instalado y no se quiere instalar aparte, la página de descargas proporciona un instalador que incluye el entorno.

En el momento de la preparación de este tutorial, la versión utilizada para OpenRefine es la 3.6, de enero de 2022, en [Windows](https://oss.sonatype.org/service/local/artifact/maven/content?r=releases&g=org.openrefine&a=openrefine&v=3.6.0&c=win&p=zip) (versión sin Java incluído, ya que estaba instalado antes) y [Linux](https://oss.sonatype.org/service/local/artifact/maven/content?r=releases&g=org.openrefine&a=openrefine&v=3.6.0&c=linux&p=tar.gz).

## Arrancar OpenRefine

En Windows, desde el directorio donde se ha descomoprimido el .zip descargado, se puede ejecutar `openrefine.exe` o `refine.bat`.

| ![openrefine.exe](./img/01_html_01_arrancar-win.png) | 
|:--:| 
| ![inicio OpenRefine en navegador](./img/01_html_01_arrancar-win-2.png) | 
| Figura 1. Arrancar OpenRefine (en Windows) |

Esto abre una consola (línea de comandos) y después la aplicación OpenRefine en el navegador por defecto, en la dirección http://127.0.0.1:3333

Si la aplicación no se abre en el navegador por defecto, se puede copiar esa misma dirección en otro navegador. Aquí hemos usado Firefox en Windows, y Firefox o Chrome en Linux.

## Extracción de un poema dado su URL

Para comenzar una extracción, hay varias posibilidades, que se ven en las opciones del menú *Create project*, que se ve en la figura 1 abajo. Entre otras, las siguientes:
- utilizar archivos que ya están en el ordenador
- Dar URLs
- Pegar el contenido del portapapeles (hemos copiado información antes con *Ctrl+C* y la pegamos aquí)

Aquí vamos a pegar los URLs que nos interesan y bajaremos el HTML de las páginas a las que corresponden.

## Extracción de múltiples poemas a partir de una lista de URLs

Para el poema individual, tomamos “Una viñeta” de Delmira Agustini: https://es.wikisource.org/wiki/Una_vi%C3%B1eta .

Como muestra la figura 2, se pega el URL (1) y después de confirmar con *Next*, podemos crear el proyecto (2). El nombre de proyecto elegido aquí es *AgustiniPoemaIndiv*.

Para que OpenRefine analize el URL correctamente, hay que elegir la opción *Parse data as Line-based text files*. Así tomará el valor de la línea completa como el URL (sin intentar dividirlo).

Se confirma la creación del proyecto con *Create project*.

|1|
|:--:|
| ![openrefine.exe](./img/01_html_02_url-portapapeles.png) | 
|**2**| 
|![iniciar proyecto](./img/01_html_02-02_create_project.png)|
| Figura 2. Copiar URL de portapapeles (1) e iniciar un proyecto (2) |

El proyecto tiene inicialmente una sola columna (*Column 1*) que contiene el URL que hemos introducido (figura 3).


| ![column 1](./img/01_html_03_column-1.png) | 
|:--:|
|Figura 3. Única columna inicial del proyecto|

La figura 4 muestra el diálogo para bajar el HTML de la página asociada al URL.

En (1), con la opción *Edit column*, disponible en el menú desplegable que se activa con la flecha de *Column 1*, se selecciona *Add columns by fetching URL)

En el diálogo de (2), hay varias partes que nos interesan:

- *New column name*: para el nombre de la nueva columna; aquí hemos elegido *htmlOriginal* 
- *Throttle delay*: el  numero de milisegundos (ms) que OpenRefine va a esperar entre peticiones de páginas al servidor. Por defecto espera 5 segundos, pero podemos cambiar este valor a algo más corto, p. ej. 1200 ms.
- On error: Hay dos opciones, *set to blank* y *store error*. *Store error* es más interesante en el sentido de que, en caso de que la operación dé un error, el mensaje de error se almacenará en la celda correspondiente de la columna. Al contrario esa celda quedará blanca.

Las opciones relacionadas con la manipulación de resultados (cuadro *Expression* y paneles *Preview*, *History*) se ven más abajo.

Aquí dejamos, dentro del cuadro *Expression*, la expresión `value`. El resultado de bajar el HTML de la página asociada el URL será almacenado en la columa *htmlOriginal* (ver figura 5) sin manipulación ulterior. 

|1|
|:--:|
| ![fetch 1](./img/01_html_04_dialogo-fetch.png) | 
| ![fetch 2](./img/01_html_04_dialogo-fetch-02.png) | 
|**2**|
|Figura 4. Bajar el HTML para el URL|

| ![fetch 1](./img/01_html_05_html-bajado.png) | 
|:--:|
|Figura 5. Nueva columna *htmlOriginal*|

A partir de este HTML, vamos a extraer metadatos del poema (autora y título), así como su texto.

Las partes siguientes del HTML nos interesan para esto (figura 6):

**(1) Autora**
```xml
<span class="ws-author">Delmira Agustini</span>
```

**(2) Título**
```xml
<title>Una viñeta - Wikisource</title>
```

**(3) Texto del poema**
```xml
<div class="poem">
<p>&#160;Tarde sucia de invierno. El caserío,   <br />
&#160;como si fuera un croquis al creyón,   <br />
&#160;se hunde en la noche. El humo de un bohío,   <br />
&#160;que sube en forma de tirabuzón;   <br />
&#160;<br />
<br />
&#160;mancha el paisaje que produce frío,  <br />
&#160;y debajo de la genuflexión   <br />
&#160;de la arboleda, somormuja el río   <br />
&#160;su canción, su somnífera canción.   <br />
&#160;<br />
<br />
&#160;Los labradores, camellón abajo,   <br />
&#160;retornan fatigosos del trabajo,  <br />
&#160;como un problema sin definición.   <br />
&#160;<br />
<br />
&#160;Y el dueño del terruño, indiferente,   <br />
&#160;rápidamente, muy rápidamente,   <br />
&#160;baja en su coche por el camellón.
</p>
</div>
```
|&nbsp;|
|:--:|
|Figura 6. Elementos HTML a partir de los cuales extraeremos la información|

Para extraer la autora, la expresión es:

```
value.parseHtml().select("span[class=ws-author]")[0].innerHtml()
```