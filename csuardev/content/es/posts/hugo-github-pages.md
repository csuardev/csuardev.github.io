+++
date = '2025-01-26T10:11:13+01:00'
draft = true
title = 'Crear un blog personal para desarrolladores con Hugo y Github Pages'
+++
Generar sitio estatico con Hugo:

generamos el proyecto en github ( si queremos que sea nuestro perfil tiene que ser nombreUsuario.github.io el nombre del repo)
-- generamos y seteamos en nuestro usuario la clave ssh para poder luego hacer los commits

hacemos un clone del proyecto y entramos
dentro generamos un nuevo proyecto de hugo usando:

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
-Entramos en la raiz del proyecto
- una vez dentro tendremos que descargar un tema, en nuestro caso usaremos una adaptacion de beautiful de jekkyl para hugo :
```bash
$ git submodule add https://github.com/halogenica/beautifulhugo.git themes/beautifulhugo
```

Esto generara todo lo necesario para el proyecto, incluido un ejemplo dentro de la carpeta themes

usamos como base el ejemplo que nos viene dentro de la carpeta theme para confirmar que todo se ha configurado correctamente, para ello hacemos:
```bash
$ cp -r themes/beautifulhugo/exampleSite/* . -iv
```
Nos preguntara si queremos sobreescribir nuestro archivo hugo.toml. Como no hemos metido ninguna configuracion propia le decimos que si.

Para confirmar si todo ha ido como deberia lanzamos el servidor de hugo que nos permitira el sitio ( y ademas permite hacer cambios en caliente y verlos al instante en el navegador)

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

Si con esto hacemos un push a origin veremos que aun no es posible ver nuestro sitio desplegado en el dominio usuario.github.io . Esto es asi porque por defecto github intenta usar jekyll para generar nuestro sitio a partir de los ficheros

Para que github Pages funcione podriamos o bien hacer que hugo generase los estaticos en la raiz en lugar de en la carpeta public ( github pages busca por defecto un index.html en el directorio raiz) o hacer que automaticamente se generen los estaticos al subir con github actions

para que cuando subamos cambios en el codigo veamos automaticamente los cambios generados ( recordemos que hugo debe compilar los fuentes y generar los estaticos que colocara en la carpeta public) 

https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site#publishing-with-a-custom-github-actions-workflow

Si no generamos el proyecto directamente en la raiz del repositorio es necesario cambiar el paso de generacion de hugo para que primero entre en la carpeta del proyecto añadiendo cd nombreProyecto && hugo...
Esto lo cambiamos al usar el github action asociado a hugo que hay precreado
