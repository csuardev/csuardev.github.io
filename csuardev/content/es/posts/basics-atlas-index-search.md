+++
date = '2025-02-16T10:11:13+01:00'
draft = true
title = 'Mongo Atlas Search Index: Una introduccion'
+++
//reference : 
prueba
https://medium.com/@sumitkessar/atlas-search-made-easy-a-summary-guide-for-developers-883c27886987
https://medium.com/@sumitkessar/atlas-search-made-easy-a-summary-guide-for-developers-883c27886987#b578
https://www.mongodb.com/developer/products/atlas/atlas-search-vs-regex/ 
https://www.mongodb.com/docs/atlas/atlas-search/performance/query-performance/
replace $text and $regex with $search
https://www.mongodb.com/docs/atlas/performance-advisor/recommend-search-text/#std-label-text-to-search
https://www.mongodb.com/docs/atlas/schema-suggestions/case-insensitive-regex/#std-label-regex-to-search


diagrama hecho en mermaid , editable:https://mermaid.live/edit#pako:eNqFUkFuwjAQ_Iq151A10AjqQyUol0hFasuthIOJlyQSsZGxK2iUv3dxkhKoqvqS2dHM7HrjClItEThkRuxz9vKeKEbn4DYNMdepK1HZQ8Ofz3SVANEs5GyhVabnMza1O3FgSxQmzRNYs8HgicWrWH2isShZrCQe15eEWZswvCS8OTSnQmWNlsBPzMX23NpG_Kohe0Wz1aYUKsUbFyqZqAbGnm_bUVCLOKv8XQLmB6opoK_3fUjtv9fa0a22mYbEDfhH3V2Y9B1sHb-G6FZC0g7-Je2tgtS9qjX4MSCAEokuJP336mxPwOZY0vY4QYlb4XY2gUTVJBXO6uVJpcCtcRiA0S7Lu8LtpbA4LwS9lrIj90J9aN0vgVdwBH4fwAl4GN1FD-MwGoXhZBINH8dRHcCXN4QBoCysNovmUfq3WX8DjOvOOQ

Hoy vamos a hablar un poco sobre mongo atlas search index, pero para ello necesito hacer una primera aclaracion, ya que este tipo de indices no esta disponible en cualquier mongo,
sino que solo esta disponible para Mongo Atlas. Esto quiere decir que si tienes mongo instalado en un vps o en una maquina local en la que desarrolas tus proyectos personales me temo que no vas a tener acceso a este tipo de indices.

### Mongo Atlas Search Index
Partamos del principio, haciendo un recordatorio rapido de que es un indice y para que los utilizamos (algunos esto les resultara super basico, pero es mas facil obviarlo si lo sabes que dejarse a gente por el camino). En resumen y sin que venga ningun 
experto en bases de datos a matarme un indice no es mas que una estructura que ayuda a mejorar la velocidad de las operaciones de bases de datos (basicamente agilizando la busqueda). Para ello se generan internamente identificadores que permiten correlacionar rapidamente un criterio de busqueda con su resultado.
Como ejemplo visual rapido se puede pensar en tener una serie de filas en una bbdd relacional en la que un campo es fecha, si vamos a buscar por ese campo sin indice tendremos que recorrer todas las filas hasta dar con el registro en el que la fecha coincide con la que buscamos. Sin embargo si 
generamos un indice para esa fecha la bbdd guardara internamente esos registros ordenados por fecha lo que permite rapidamente descartar ir a buscar la fecha ya que almacenara una estructura de datos (el indice en si) con las fechas ordenadas lo cual creo que puede verse rapidamente como permite agilizar la consulta.
Al final no deja de ser el equivalente al indice de un libro con muchos capitulos, puedes ir pagina a pagina hasta encontrar un titulo de capitulo y ver si es el que buscas, o puedes ir a la pagina del indice y buscar el indice que necesitas y desde ahi ir a la pagina que corresponda.

Una vez aclarado esto vamos a hablar en concreto de Atlas Search Index y como funciona a un nivel general(tampoco soy yo ningun experto en bbdd ni en mongo).
Si nos vamos a la definicion formal que nos da Atlas de este indice nos dicen que *"Atlas Search es una búsqueda de texto completo integrada en MongoDB Atlas que le brinda una experiencia perfecta y escalable para crear funciones de aplicaciones basadas en relevancia. Atlas Search, desarrollado sobre Apache Lucene, elimina la necesidad de ejecutar un sistema de búsqueda independiente junto con su base de datos."*
Quitando la palabreria comercial lo que quiere decir es que es un sistema de busquedas de texto complejo que se sustenta sobre apache lucene (quien haya usado kibana o similar se trata del mismo motor del que partir a grandes rasgos), y que ademas esta perfectamente integrado en mongo dentro de atlas lo que hace que no necesites sistemas anexos a la bbdd para las busquedas complejas.

En este punto quien haya usado mongo podra ver que hasta cierto punto esta funcionalidad esta parcialmente cubierta con las busquedas de tipo $Text , que permiten crear un indice para campos de tipo texto y posteriormente buscar por ellos de forma eficiente. Tambien parte de esta funcionalidad esta cubierta 
por busquedas $Regex que permite aplicar dentro de mongo toda la potencia de las expresiones regulares para busqueas parciales o de patrones de texto, eso si, con un coste computacional considerable ya que, en principio, este tipo de busquedas no se realizan a traves de un indice.
Vamos a ver esta comparativa con algo mas de detalle para ver que nos puede hacer elegir Atlas Search Index frente a estas dos alternativas que son quizas mas conocidas y bastante comunes.

## Rendimiento frente a busquedas $text o $regex
Como comentabamos anteriormente, pese a que ya existen otras formas de realizar busquedas de texto dentro de las colecciones de mongo estas tienen importantes limitaciones para casos de uso complejos o donde el rendimiento es un factor determinando.
Empezando por las limitaciones de estos sistemas tendriamos el tener que elegir segun si necesitamos busquedas exactas, en cuyo caso podemos utilizar $text como metodo de busqueda y no necesitariamos nada mas, o si por el contrario necesitamos permitir busquedas parciales, lo cual podriamos lograr mediante las correspondientes expresiones regulares aplicadas con busquedas de tipo $regex.
Como vemos con estos dos tipos de busqueda solo cubrimos dos casos de uso, que ademas son bastante sencillos, y aunque es cierto que se pueden ampliar para cubrir hasta cierto punto casos de uso mas complejos, la realidad es que entramos en querer usar las herramientas incorrectas para un trabajo en el que no rinden como deberian.

frente a $text y $regex mongo atlas search index lo que crea realmente por debajo es un indice inverso, es decir, una especie de correlacion de cada palabra con el lugar en el que se encuentra(muy simila a lo que harias si quisieras montar un motor de busqueda).Sin embargo cuando usamos busquedas de tipo $text o $regex dentro de mongo lo esta pasando debajo del capo es que esta haciendo un escaneo de los documentos ya sea para buscar el $texto o el patron de expresion regular que estamos buscando,esto hace que altas search index sea mucho mas rapido y eficiente a nivel de recursos, mientras que si que tiene un mayor consumo de almacenamiento ya que debe guardar esa tabla de correlaciones que usa como indice para buscar de forma rapida y eficaz.
//notes differences in indexing process
 Indexing Process

$text index:
Tokenization: Breaks down text into individual words (tokens).
Stemming: Reduces words to their root form (e.g., "running" becomes "run").
Stop word removal: Excludes common words like "the," "a," "is" that don't add much search value.
Creates an inverted index: Maps each word to the documents where it appears.
$search index:
More sophisticated analysis: Lucene provides a wider range of text analysis options, including stemming, stop word removal, synonyms, and more.
Advanced indexing structures: Lucene uses optimized data structures for fast search and retrieval. 
Flexible configuration: You have more control over how text is analyzed and indexed.

![Mongo Atlas Search Index Inverted Diagram](/images/mongo-atlas-search-index/mongo-inverted-index-diagram.png)
- features
- Full-text search
- Fuzzy search
- Typeahead Autocomplete
- text Scoring
- text highlighting
- Support for the five most common Lucene analyzers: standard, simple, whitespace, language, keyword
- Support for multiple data types: text, numerics, dates, geospatial, boolean, and objectID
- Support for 36 languages (you can see the full list in our documentation)
- Support for Facet in Search

|Feature             |Atlas Search                                    |$text Search (with Text Index)    |$regex Search (typical)                                       |
|--------------------|------------------------------------------------|----------------------------------|--------------------------------------------------------------|
|Indexing            |Inverted Index (highly optimized)               |Text Index (optimized for text)   |No Index (usually)                                            |
|Performance         |Excellent (fast, scalable)                      |Good (generally faster than regex)|Poor (slow, resource-intensive)                               |
|**Fuzzy Matching**      |**Yes (handles typos, misspellings)**               |**No (exact matching)**               |**Limited (requires complex regex)**                              |
|**Partial Matching**    |**Yes (finds parts of words)**                      |**No (whole word matching)**          |**Yes (but can be inefficient)**                                  |
|**Synonyms**            |**Yes (can be configured)**                         |**No**                                |**No**                                                            |
|Relevance Ranking   |Yes (scores results)                            |Yes (scores results)              |No                                                            |
|Faceting            |Yes (for filtering and refinement)              |No                                |No                                                            |
|**Autocomplete**        |**Yes (for suggestions)**                           |**No**                                |**Difficult to implement effectively**                           |
|Multilingual Support|Yes                                             |Yes (but requires configuration)  |Limited                                                       |
|Deployment          |MongoDB Atlas (cloud-based)                     |Any MongoDB deployment            |Any MongoDB deployment                                        |
|Use Cases           |Complex search, large datasets, high performance|Basic text search, medium datasets|Pattern matching, small datasets, specific string manipulation|
|Resource Usage      |Moderate (dedicated service)                    |Moderate (index size)             |High (CPU intensive)                                          |
|Complexity          |More complex setup, powerful features           |Simpler setup, basic features     |Simple for basic cases, complex for advanced patterns         |

Ademas Mongo ha dejado de realizar desarrollos y mejoras en las funcionalidades de $text y $regex desde 2015 y ha centrado sus esfuerzos de mejora en busquedas basadas en relevancia se centran ahora mismo en $search (esto es algo a tener en cuenta en proyectos con una vida futura larga, que podran beneficiarse de las mejoras que se vayan metiendo)
## Funcionalidades
Las busquedas $text y $regex en mongo aunque son interesantes y pueden cubrir casos de uso relativamente sencillo tienen capacidades de configuracion limitadas.
Por su parte atlas search index ofrece por defecto una serie de caracteristicas y posibilidades de configuracion muy potentes:

//fin notas

### Caso de uso

Mientras que las busquedas de $text y $regex de mongo pueden servir cuando la busqueda es una parte menor de la funcionalidad, o una funcionalidad secundaria dentro de la aplicacion
atlas search index es una solucion completa para tener un motor de busquedas embebido dentro de tu base de datos.


### Ejemplo practico basico
//caso teorico para el articulo , que sea sencillo para entenderse , pero complejo como para ver las ventajas frente a $regex o $text


