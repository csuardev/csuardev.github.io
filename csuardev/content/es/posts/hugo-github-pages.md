+++
date = '2025-01-26T10:11:13+01:00'
draft = false
title = 'Crear un blog personal para desarrolladores con Hugo y Github Pages'
+++
Para generar nuestra pagina personal y alojarla en github pages lo primero que haremos es generar el proyecto en github ( si queremos que sea nuestro perfil tiene que ser nombreUsuario.github.io el nombre del repositorio)

Lo siguiente si no lo tenemos previamente configurado sera configurar nuestra clave ssh en github para poder hacer los push de nuestros cambios en los proyectos.


#### Generar y configurar la clave ssh 

generar una clave ssh para github:
```bash 
ssh-keygen -t ed25519 -C "your_email@example.com"
```
[documentacion oficial github](https://docs.github.com/en/enterprise-server@3.14/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

Copiamos la clave ssh publica que acabamos de generar (archivo .pub que se encuentra dentro de ~/.ssh/) y lo pegamos dentro de nuestro perfil de github en el apartado de claves ssh. 

Podemos comprobar si funciona y nos identifica correctamente con el siguiente comando:
```bash
ssh -T git@github.com
```


#### Instalar Hugo

ver prerequisitos y otros metodos de instalacion en https://gohugo.io/installation/

En nuestro caso lo vamos a instalar en linux(mint) y usando los prebuilt binaries.
Para ello vamos a la pagina de releases de Hugo y descargams la version que nos interese, en nuestro caso usaremos la mas reciente en el momento de escribir este post que es la [0.142.0](
https://github.com/gohugoio/hugo/releases/tag/v0.142.0)
Descomprimimos el archivo descargado usando:
```bash
tar -xvzf filname
```
Esto nos genera tres archivos. Nos interesa el que contiene Hugo. Si el archivo contiente el nombre de la version podemos renombrar el archivo a hugo 
```sh
mv nombreArchivo nuevoNombre
```
Y movemos este archivo renombrado a /usr/local/bin 
```sh
mv hugo /usr/local/bin/
```
Esto nos permite invocar hugo directamente desde la terminal
Si el comando mv nos da un error de permisos tendremos que lanzarlo como sudo
```sh
sudo mv hugo /usr/local/bin/
```


#### Generar una web basica con Hugo

Hacemos un clone del proyecto, entramos y generamos un nuevo proyecto de Hugo usando:

```bash
hugo new site nombreProyecto
```

Esto nos dara como resultado algo similar a esto: 
```bash
Congratulations! Your new Hugo site was created in /home/user/Documents/dev/hugoProjects/csuardev.github.io/csuardev.

Just a few more steps...

1. Change the current directory to /home/user/Documents/dev/hugoProjects/csuardev.github.io/csuardev.
2. Create or install a theme:
   - Create a new theme with the command "hugo new theme <THEMENAME>"
   - Or, install a theme from https://themes.gohugo.io/
3. Edit hugo.toml, setting the "theme" property to the theme name.
4. Create new content with the command "hugo new content <SECTIONNAME>/<FILENAME>.<FORMAT>".
5. Start the embedded web server with the command "hugo server --buildDrafts".

See documentation at https://gohugo.io/.
```
Seguimos los pasos indicados:
- Entramos en la raiz del proyecto
- una vez dentro tendremos que descargar un tema, en nuestro caso usaremos una adaptacion de beautiful de jekkyl para hugo (Si preferimos seguir el tutorial oficial podemos usar el que nos recomienda o cualquier otro que nos guste de los muchos disponibles en la web de [temas de Hugo](https://themes.gohugo.io/)) :
```bash
$ git submodule add https://github.com/halogenica/beautifulhugo.git themes/beautifulhugo
```

Esto generara todo lo necesario para el proyecto, incluido un ejemplo dentro de la carpeta themes.

Usamos como base el ejemplo que nos viene dentro de la carpeta theme para confirmar que todo se ha configurado correctamente. Para ello copiamos el ejemplo en nuestro directorio raiz de hugo:
```bash
$ cp -r themes/beautifulhugo/exampleSite/* . -iv
```
Nos preguntara si queremos sobreescribir nuestro archivo hugo.toml. Como no hemos metido ninguna configuracion propia le decimos que si.

Para confirmar si todo ha ido como deberia lanzamos el servidor de hugo que nos permitira ver la pagina web en nuestro local renderizada ( y ademas permite hacer cambios en caliente y verlos al instante en el navegador)

```bash
hugo serve
```
Esto nos dara como resultado algo similar a esto:
```bash
Start building sites … 
hugo v0.142.0-1f746a872442e66b6afd47c8c04ac42dc92cdb6f linux/amd64 BuildDate=2025-01-22T12:20:52Z VendorInfo=gohugoio


                   | EN   
-------------------+------
  Pages            |  36  
  Paginator pages  |   2  
  Non-page files   |   0  
  Static files     | 185  
  Processed images |   0  
  Aliases          |   9  
  Cleaned          |   0  

Built in 150 ms
Environment: "development"
Serving pages from disk
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1) 
Press Ctrl+C to stop
```
Podemos ver lo que hemos generado accediendo a la url que aparece en la consola

Hugo serve admite varias opciones de parametros que permiten modificar su comportamiento, decidiendo por ejemplo que no cachee ciertos archivos o que muestre los drafts para poder revisarlos en local antes de convertirlos en posts definitivos.


#### Configurar Github Pages para funcionar con Hugo

Si con lo que hemos generado hacemos un push a origin veremos que aun no es posible ver nuestro sitio desplegado en el dominio usuario.github.io . Esto es asi porque por defecto github intenta usar jekyll para generar nuestro sitio a partir de los ficheros.

Para que github Pages funcione podriamos o bien hacer que hugo generase los estaticos en la raiz en lugar de en la carpeta public ( github pages busca por defecto un index.html en el directorio raiz) o hacer que automaticamente se generen los estaticos al subir con github actions para que cuando subamos cambios en el codigo veamos automaticamente los cambios generados (hugo debe compilar los fuentes y generar los estaticos que colocara en la carpeta public).

[Video con mas informacion sobre como funciona Hugo](https://www.youtube.com/watch?v=ZFL09qhKi5I)

[Documentacion de github sobre la configuracion de github actions para publicar sitios estaticos en github pages](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site#publishing-with-a-custom-github-actions-workflow)

Para activar github actions con hugo debemos ir al settings del proyecto de github y buscar en el apartado pages:

![github pages config](/images/hugo-github-pages/github-config-pages.png)

Desde esta pagina inicialmente nos aparecera seleccionado en el desplegable 'Deploy from a branch' y debemos cambiarlo a 'Github Actions'. Al hacerlo nos dara la opcion de seleccionar un workflow preconfigurado, buscamos Hugo y seleccionamos el que nos aparece. Esto generara en el repositorio una carpet .github/workflows dentro de la que se encuentra la configuracion de despliegue de nuestro codigo.

Si no generamos el proyecto directamente en la raiz del repositorio (como es nuestro caso) es necesario cambiar el paso de generacion de hugo en la configuracion de github actions para que primero entre en la carpeta del proyecto añadiendo cd nombreProyecto && hugo... de forma que el paso de generacion de hugo queda asi:
```sh
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          cd ./nombreProyecto && hugo \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
```
Ademas debemos editar el ultimo paso para indicarle que la carpeta donde se deben generar los estaticos no esta en la raiz sino dentro de nuestro nombre de proyecto:
```sh
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./nombreProyecto/public
```

Una vez que hemos hecho esto, si lo hacemos en local deberemos hacer un push y esto generara un nuevo build que se lanzara con la nueva configuracion. Si hemos hecho el cambio directamente desde el editor de github al generar el commit se lanzara el despliegue y si todo ha ido bien podremos ver la web de ejemplo como la que veiamos en local en la url nombreProyecto.github.io . El estado de los diferentes deploys los podemos ver en varios sitios, pero el mas sencillo es en el repositorio de github donde nos aparecera en la columna derecha un apartado con los deployments. Nos aparecera el ultimo y ademas podremos consultar los anteriores.

![deployments from github](/images/hugo-github-pages/github-deployments.png)

Si todo ha ido bien ya podemos generar nuestro primer contenido propio. Para esto podemos usar el comando hugo new content/ruta/contenido/nombre_post.md, o bien podriamos crear directamente un archivo .md en el directorio dentro de contenido que elijamos.

Cuando usamos el comando hugo new content/... hugo aplicara los templates del tema en cuestion, por lo que dependera del tema que estamos usando lo que nos creara, pero de forma normal el comando creara un archivo .md en la carpeta correspondiente con una *cabecera* (front matter) con la fecha, titulo, si se trata de un draft o no , tags...

Ejemplo archivo .md con su *cabecera* :
```md
---
title: About me
subtitle: Why you'd want to go on a date with me
comments: false
---

My name is Inigo Montoya. I have the following qualities:

- I rock a great mustache
- I'm extremely loyal to my family

What else do you need?

### my history

To be honest, I'm having some trouble remembering right now, so why don't you just watch [my movie](https://en.wikipedia.org/wiki/The_Princess_Bride_%28film%29) and it will answer **all** your questions.
```

En nuestro caso siguiendo con el ejemplo para beautiful hugo generamos el post en la ruta content/posts/nombre-articulo.md . Por defecto el tema nos genera un titulo a partir del nombre del archivo, pero somos totalmente libres para cambiarlo. En este archivo .md podemos usar markdown normal que luego renderizara a html en la carpeta public.

En el caso del tema beautiful hugo viene preconfigurado para que sea sencillo mantener una web multidioma, de forma que podemos indicar en el fichero hugo.toml la jerarquia de idiomas y luego bastara con crear contenido dentro de la ruta de cada idioma para que solo se muestre cuando se esta visualizando la pagina en ese idioma.
```toml
[languages]
  [languages.en] 
    contentDir = "content/en"
  [languages.es]
    contentDir = "content/es"
```
```
content/      content/        
└── en/       └── es/        
    ├── page/     ├── page/ 
    └── post/     └── post/ 

```
Ademas, si se mantienen los nombres del fichero entre ambos (o varios) idiomas se enlazaran para que aparezcan uno como traduccion del otro.
```
content/              content/        
└── en/               └── es/        
    ├── page/         ├── page/ 
    |   └── about.md      └── about.md
    └── post/         └── post/ 

```

Mas informacion sobre otras configuraciones que ofrece el tema [aqui](https://github.com/halogenica/beautifulhugo)

A partir de aqui todo lo que queda es jugar con las opciones que ofrece hugo, probar diferentes temas que ofrecen diferentes configuraciones por defecto que nos pueden facilitar el trabajo segun el tipo de sitio que queramos crear, e incluso modificar alguno a nuestro gusto si algo no nos temina de encajar con nuestra forma de trabajar.



