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
### MongoDB vs Mongo Atlas

### Mongo Atlas Search Index
 
//notas
Atlas Search is an embedded full-text search in MongoDB Atlas that gives you a seamless, scalable experience for building relevance-based app features. Built on Apache Lucene, Atlas Search eliminates the need to run a separate search system alongside your database.
Hay que tener en cuenta que mientras que es posible utilizar $regex y $text on premise o en entornos locales, $search solo esta disponible
en Mongo Atlas y por el momento no se ofrece en las instancias serverless.

## Rendimiento frente a busquedas $text o $regex
frente a $text y $regex mongo atlas search index lo que crea realmente por debajo es un indice inverso, es decir, una especie de correlacion de cada palabra con el lugar en el que se encuentra(muy simila a lo que harias si quisieras montar un motor de busqueda).Sin embargo cuando usamos busquedas de tipo $text o $regex dentro de mongo lo esta pasando debajo del capo es que esta haciendo un escaneo de los documentos ya sea para buscar el $texto o el patron de expresion regular que estamos buscando,esto hace que altas search index sea mucho mas rapido y eficiente a nivel de recursos, mientras que si que tiene un mayor consumo de almacenamiento ya que debe guardar esa tabla de correlaciones que usa como indice para buscar de forma rapida y eficaz.

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


