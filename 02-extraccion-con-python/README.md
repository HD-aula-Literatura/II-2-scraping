Extracción de contenido (*scraping*) con el lenguaje de programación Python
=====

Python es un lenguaje de programación muy utilizado en proyectos de humanidades digitales.

Como vimos en el capítulo, para hacer scraping, es necesario poder efectuar varias operaciones de manipulación de contenido
- Acceder a URLs
- Analizar el contenido de los documentos accesibles a través de esos URLs (p. ej. contenido en formato HTML)
    - Esto implica métodos para manipular la estructura de datos inducible a partir del documento, p. ej. un árbol HTML o XML, o bien cadenas de caracteres
- Poder guardar el contenido extraído de forma permanente (p. ej. en archivos, que es lo más simple y es lo que veremos aquí), o en una base de datos
    - A su vez, esto implica poder crear archivos y quizás directorios
    - Esto puede implicar también métodos para escribir de forma fiable en formatos de archivo ampliamente utilizados, como ficheros delmitados (CSV, TSV) o documentos utilizados por una hoja de cálculo como Excel
    
Python es un lenguaje adecuado para este tipo de operaciones

## ¿Por qué usar un lenguaje de programación par hacer *scraping*?

Si bien una herramienta como OpenRefine tiene funciones potentes para el scraping, usar un lenguaje de programación autónomo es más flexible.

Por ejemplo, usar un lenguaje de programación permite una mayor automatización. En OpenRefine hemos exportado los resultados de la extracción "a mano", a través de opciones que debemos clicar en la interfaz gráfica. Con un lenguaje de programación, podemos escribir código que se ocupará de hacer las exportaciones deseadas (en uno o varios formatos), creando nombres de archivos de salida dinámicamente

## Cómo usar los materiales

Se proporcionan notebooks Jupyter. Son documentos que combinan texto (y quizás imágenes u otros archivos) con secciones de código. Las secciones de código se pueden usar simplemente para leer el código, pero son también ejecutables si abrimos el notebook desde un entorno interactivo que gestione notebooks Jupyter (instalado en nuestro ordenador o en una plataforma en la nube).

Al principio de cada notebook se da un enlace a un entorno de la plataforma [Binder](https://mybinder.org/) que permite usarlos de forma interactiva ejecutando código y añadiendo nuestras propias secciones también. 
