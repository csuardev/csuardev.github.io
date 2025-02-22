+++
title = 'Unit Test Junit'
subtitle: ""
draft = true
author: ""
description: ""
date: 22 de febrero de 2025
image: ""
tags: []
+++

## Que son
Los test unitarios son un tipo de prueba que pretende comprobar que el comportamiento de un pequeño trozo de codigo, de forma aislada y para unos parametros controlados. Que constituye esa unidad que tiene entidad suficiente para ser probada de forma aislada es una de las principales variables para realizar test unitarios de calidad y que realmente sean utiles, como veremos a continuacion.

## Para que sirven
Los test unitarios cumplen a grandes rasgos, y simplificando mucho, dos funciones. Por una parte permiten confirmar que para los casos de uso definidos el comportamiento es el esperado, de forma repetible y automatizable. Asi, en este sentido los test unitarios vienen a susituir las clasicas pruebas manuales en las que ejecutas el programa, le pasas unos parametros, y confirmas si el resultado es el que esperabas. Por otra parte, otra funcion importante de los tests unitarios es servir de confirmacion de que el comportamiento no ha cambiado tras cambios en el codigo, por ejemplo tras una refactorizacion, cambio de version de alguna libreria, etc... Esto es lo que llamamos pruebas de regresion, y es especialmente importante en proyectos grandes donde varias personas estan modificando diferentes partes de codigo y hay que asegurar que el cambio de una persona no afecta a otra parte del codigo.

Hoy en dia la mayoria de proyectos con un minimo de entidad aplican test unitarios en mayor o menor medida. Pero no todos los test unitarios son iguales, como todo en el mundo de la programacion, se pueden hacer bien, regular, o directamente mal. Vamos a ver algunos criterios basicos para generar unos buenos test unitarios, o que al menos sean utiles para la vida del proyecto.

## Consideraciones y buenas practicas
Una consideracion muy importante es que la utilidad de un test unitario va a estar muy limitada por el codigo que se pretende probar. Es decir, si tenemos un codigo altamente acoplado, y cuyas funcionalidades no estan correctamente segmentadas va a ser muy complicado crear test unitarios que cumplan con los criterios que vamos a nombrar. Para evitar estas cosas se pueden usar enfoques inversos como Test Driven Development (TDD link) donde se generan primero los tests que definen el comportamiento esperado de la aplicacion y luego se realiza el codigo, esto fuerza en gran medida que se sigan los principios SOLID, especialmente la separacion de responsabilidades, pero de eso ya hablaremos en otro post con un poco mas de detenimiento.

Como hemos comentado previamente todos los test unitarios no nacen iguales, algunos nacen llamandose test unitarios, pero siendo hijos bastardos de estos. Para que un test unitario se pueda considerar como tal y sea al menos minimamente util debe cumplir las siguientes caracteristicas generales:

- **Aislamiento:** La base de los test unitarios y la norma fundamental es la unicidad de la prueba, es decir, debemos probar la unidad minima posible, y ademas, cada caso de uso debe probarse en un test separado. En un ejemplo sencillo si tenemos un metodo calcular que acepta dos enteros como parametro debemos generar por separado un test para el caso de dos enteros positivos y otro para el caso de dos enteros negativos ( luego veremos por que esto es tan importante con ejemplos).

- **Repetibilidad:** Los test deben poderse lanzar varias veces, en cualquier orden sin que eso altere el resultado. Para esto en algunos casos es necesario usar *mocks*, es decir, debemos simular el comportamiento de las partes que generen cambios de estado. Por ejemplo las interacciones con bbdd de forma que no debe afectar si ejecutamos primero el test de borrado del registro o el de generar el registro, en ambos casos simularemos la interaccion con la base de datos y haremos que se comporte segun necesitemos para el test (nos interesa por ejemplo revisar el comportamiento de un update de base de datos si simulamos que el registro no existia y que por lo tanto hay una inconsistencia en los datos). Por si alguno esta pensando que esto es hacer trampas y que no estamos probando de verdad la aplicacion, recordemos que aqui se prueba el codigo en si, no la interaccion con otros sistemas, para eso hariamos test de integracion que son tambien necesarios y que ya veremos con mas detalle en otro articulo.

- **Velocidad:** Este es un apartado que solemos olvidar, pero que es muy importante porque permite ejecutarlos frecuentemente durante el desarrollo. Si los tests son lentos los desarrolladores solo los ejecutaran al acabar de programar o cuando se lancen de forma automatica en el pipeline de despliegue (si es que asi esta configurado). Los tests deben ser siempre lo mas rapidos posibles para que se ejecuten lo mas frecuentemente posible. En un mundo ideal deberiamos poder ejecutar rapidamente los tests que afectan a las funcionalidades que estamos tocando tras cada cambio sin que nos genere pereza el esperar a ver el resultado. Esto permite iterar rapidamente sobre el codigo y detectar errores en fases iniciales del desarrollo, y evitando que se malgaste el tiempo avanzando en una solucion que ya desde el principio no mantiene el funcionamiento de la aplicacion.

- **Claridad:** Los tests deben ser lo mas claros y consisos posible, de forma que cualquiera que vea el test entienda que es lo que se esta probando y como se esta probando. Esto ayuda a que cuando fallen sea facil determinar que caso concreto se estaba probando y como se estaba probando. No hay nada peor que hacer un cambio, ver que un test falla y al revisar no tener muy claro realmente que es lo que pretende probar el test y si el error es del codigo que has generado o del diseño del test. Este apartado esta fuertemente ligado al siguiente.

- **Nombres descriptivos:** Una de las formas mas faciles que tenemos de hacer que cuando un test falla sepamos rapidamente que se estaba intentando probar es el nombrado de los test. No es lo mismo que un test se llame test_1 a que se llame test_suma_enterosPositivos_ok. El nombre debe ser lo mas descriptivo posible sin pasarnos, una buena formula para nombrar los tests es usar **nombreMetodo_casoDeUso_resultado**.
## Ejemplos con Junit

