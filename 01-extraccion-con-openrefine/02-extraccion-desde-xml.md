# Extracción de contenido en XML con OpenRefine

- [Introducción: XML](#s-intro)
  - [Sintaxis XML](#ss-sintaxis)
- [Extracción con OpenRefine](#s-manipular-xml)
  - [Metadatos](#ss-metadatos)
  - [Texto del poema](#ss-texto)
- [Exportar la información extraída](#ss-guardar-dataframe)

<a name="s-intro"></a>

# Introducción: XML

Como se vio en el capítulo, XML (eXtensible Markup Language) es un lenguaje utilizado para representar
información de forma estructurada. Hablamos de él aquí ya que algunos documentos obtenidos por scraping pueden estar en XML, sin embargo las tecnologías XML se usan más típicamente para manipular corpus ya limpios y que han sido representados en XML. Como el HTML, se basa en una estructura arbórea, expresable mendiante una cadena de texto, en la que el contenido está incluido dentro de pares de etiquetas anidadas, y las etiquetas de abertura pueden tener atributos. Mientras que en HTML el conjunto de etiquetas posible está predefinido, y sus funciones se refieren a la estructuración y presentación de documentos web, en XML cada persona puede definir sus propias etiquetas (es extensible) y crear su propia especificación de marcado. Una especificación XML muy utilizada en humanidades es TEI (*Text Encoding Initiative*).

El contenido presentado en XML es contenido estructurado: Las etiquetas XML y sus atributos identifican el contenido al que están asociadas, de forma que podemos manipularlo haciendo referencia a estas etiquetas y atributos. Cuando hacemos *scraping*, típicamente pasamos de contenido no estructurado (p. ej en HTML o en texto plano) a contenido estructurado. No obstante, puede haber contenido en XML como parte de los contenidos web que manipulamos con el scraping, con lo cual se menciona el formato aquí.

Hay tecnologías específicas para manipular documentos XML, como XPath, XSLT o XQuery y entornos para este fin, com Oxygen o BaseX. OpenRefine tiene algunas funciones para extraer contenido de fuentes XML. Al igual que el HTML, un documento XML es una estructura arbórea expresable mediante una cadena de texto. OpenRefine proporciona funciones para manipular el árbol del documento y poder extraer la información pertinente, presentaremos algunas aquí.

<a name="ss-sintaxis"></a>

## Sintaxis

Como se ha visto, para representar el árbol XML mediante una cadena de texto, se envuelve el contenido con pares de etiquetas anidables. Hay una etiqueta inicial de la que las demás descienden, representa el nodo raíz, en nuestro [ejemplo](#ejemplo-xml) es `<poema>`. Las etiquetas de abertura pueden llevar atributos; el valor del atributo va entre comillas o apóstrofes. Puede haber también etiquetas vacías o de autocierre, como `<fecha/>` en el [ejemplo](#ejemplo-xml).


Como se ve en el [ejemplo](#ejemplo-xml), un "prólogo" puede preceder al nodo raíz, con la forma `<?xml version="1.0" encoding="UTF-8"?>`, en él se especifica la versión y la codificación de caracteres.

Como en HTML, en XML hay también unas entidades predefinidas, cuyo uso es obligatorio en XML. Son las siguientes:

|Carácter|Entidad|
|:---:|:---:|
|<|&amp;lt;|
|>|&amp;gt;|
|"|&amp;quot;|
|'|&amp;apos;|
|&|&amp;amp;|

Es necesario usar entidades para representar estos caracteres, porque estos caracteres, de forma literal (no como entidades) se usan ya para definir la sintaxis XML cuando representamos el árbol XML como cadena de texto. Como se ve en el [ejemplo](#ejemplo-xml) abajo, `<` abre una etiqueta y `>` la cierra. Las comillas o apóstrofes se usan para los valores de los atributos. Y `&` se usa justamente para definir entidades. Así que para escribir estos signos como caracteres, no como integrantes de la sintaxis, hay que usar las entidades.

XML permite usar comentarios, que son ignorados por el parseador (el analizador que manipula el árbol del documento). Los documentos se introducen entre `<!-- -->` (misma sintaxis que los comentarios en HTML). Como se ve en el [ejemplo](#ejemplo-xml), dentro de los comentarios no hace falta usar entidades XML.


<a name="frag-ejemplo-xml"></a>

**Ejemplo de documento expresado en XML**
<html><span style="font-size:small">Elaboración propia</span></html>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<poema>
  <metadatos>
    <autoria>
      <nombre>Guillermo Carlos</nombre>  
      <apellido>Guillermos</apellido>
    </autoria>
    <!-- el título es "fruta & frigo's" -->  
    <titulo>fruta &amp; frigo&apos;s</titulo>
    <fecha/>  
  </metadatos>
  <texto>
    <titulo>fruta &amp; frigo&apos;s</titulo>
    <estrofa tipo="haiku">
      <verso>come la fruta</verso>
      <verso>del refrigerador</verso>
      <verso>muy deliciosa</verso>
    </estrofa>
  </texto>
</poema>

```

<a name="s-manipular-xml"></a>

# Extracción con OpenRefine

Wikisource también puede ofrecer su contenido en XML, a través de comandos de su API (Application Programming Interface o interfaz de programación de aplicaciones, es decir, conjunto de instrucciones para interaccionar con un programa a partir de otro programa).

Usaremos el URL que permite a la API de Wikisource devolver en XML la página del poema ya visto en el capítulo, "Una viñeta" de Delmira Agustini. El URL es https://es.wikisource.org/w/api.php?action=parse&page=Una_vi%C3%B1eta&format=xml.

Procedemos de la misma forma que para bajar una página en HTML. Vamos a crear un proyecto a partir del URL y bajar el contenido asociado a este URL (las etapas se describen en la sección 4.1 del capítulo).

Asumimos aquí que estas etapas ya se han efectuado, y que tenemos dentro de nuestro proyecto OpenRefine una columna llamada *xml* que contiene el XML a analizar.

A partir de esta columna, extraemos el título y autora del poema, así como su texto.

Si miramos el XML de la columna, nos interesan los fragmentos siguientes:

**Para la autora**

```xml
<links><pl exists="" ns="106" xml:space="preserve">Autor:Delmira Agustini</pl></links>
```

**Para el título**

```xml
<parse displaytitle="Una viñeta" pageid="27121" revid="1193682" title="Una viñeta">
```

**Para el texto**
```xml
<text xml:space="preserve"> ... </text>
```

En los casos del título y de la autora se ve claramente la localización de la información. En el caso del texto del poema, la API de Wikisource no da un campo específico con su contenido. Lo que hace es dar, dentro del elemento `<text>`, una cadena de texto que expresa una parte del contenido principal del cuerpo de la página en HTML (la parte central con el poema y algunos de sus metadatos). Así que debemos extraer esta cadena, crear un árbol HTML y extraer el texto del poema a partir de este. No es el uso más típico de XML pero nos permite combinar los conocimientos vistos en el capítulo.

<a name="sss-metadatos"></a>

Extraemos la autora con la expresión [GREL](https://docs.openrefine.org/manual/grelfunctions) siguiente:

```
value.parseXml().select("links")[0].select("pl")[0].xmlText().replace('Autor:','')
```

Nos ocupamos ahora del texto. Primero extraemos la parte central del cuerpo de la página Wikisource que contiene el poema con alguna información más (como se ha dicho, la API no divide el contenido de forma más detallada). Para esto usamos la expresión [GREL](https://docs.openrefine.org/manual/grelfunctions) siguiente:

```
value.parseXml().select("parse")[0].xmlAttr("title")
```
Ya que esta cadena expresa el HTML de la página, la analizaremos con el parseador de HTML y extraeremos el texto del poema, usando las mismas operaciones que ya se vieron en el capítulo.

```
value.parseHtml().select("div[class=poem]")[0].select("p")[0].innerHtml().unescape("html").split("<br>"), verso, verso.trim()).join("\n")
```

<html>
<!--
4.Create new column textoPoema based on column xml by filling 1 rows with grel:forEach(value.parseXml().select("text")[0].ownText().parseHtml().select("div[class=poem]")[0].select("p")[0].innerHtml().unescape("html").split("<br>"), verso, verso.trim()).join("\n")
-->
</html>
