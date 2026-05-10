#####  EJERCICIO 1

Una vez clonado el repositorio, creo la carpeta .git/workflows que es donde por defecto se deben almacenar los workflows.

El propósito es que cuando se produzcan ciertos eventos, se disparen cierts workflows que constarán de algunos pasos y en cada paso se ejecutarán algunas acciones.

Para ello se genera el fichero ci.yml dentro de la carpeta workflows.

El nombre del workflow es ci-integracion continua.

Los eventos que provocarán que se dispare el workflow son:

	- cuando en el contenido la carpeta hangman-front y sus subcarpetas /ficheros se produzca 	un push en la rama main
	- cuando se produzca una pull-request desde una rama (que en este caso yo he usado la rama ramaworkflowinicial) a la rama main 

El conjunto de acciones que se llevarán a cabo en esos casos serán 2:

* Paso bajarme cod fuente
	La acción que se realizará aquí está  predefinida y es una action de git que se llama ‘checkout’ y es la versión 6. Lo que realizará será la descarga del código fuente a la máquina ubuntu usada.

* Paso hacer build y test de la aplicación
   
    Para la parte del proyecto que está en el directio hangman-front se ejecutarán las siguientes acciones:
        - instalar las dependencias del proyecto teniendo en cuenta las versipones especificadas en el fichero package-lock.json de la carpeta hangman-front del proyecto. También se ejecutarán los scripts definidos, ‘build’, para para generar los archivos en producción  y  ‘test’ para llevar a cabo los tests unitasrios definidos.

Provoco la ejecución con un cambio en  un fichero de la subcarpeta src

Así, compruebo que se ha descargado el código fuenete y se ha hecho el test, aunque el test indica que no ha sido satisfactorio.

#####  EJERCICIO 2

Tras haber realizado el Ejercicio 1 habré hecho el merge con la rama main y habré hecho git pull.

Me creo rama cd-entrega continua y creo el cd.yml.

En el fichero, añado que el worflow se ejecute de manera manual. Para ello utilizo la directiva "workflow_dispatch" . Así el job solo se ejecutará de forma manual a través del entorno gráfiuco del VSCode.

Lo que se pretende es hacer una la build de la imagen de docker y subirla al Registry d eGithub. (publicarla).

El job se llamará jobentrega y se ejecutará en una máquina  ubuntu, con la últim vesión , como antes.

- El primer paso, llamado ‘Paso bajarmee código fuente’  descargará el código fuente del repositorio, usando una acción de github llamada "checkout" en su versión 6.

- El segundo paso será que  diga la version de docker que tiene la máquina. Se ejecutará el comando docker -v.

Para hacer el build, se epodría hacer  usando docker build. Pero es mejor opción  usar una action( login-action)  que me permite hacer login en un registry de Docker.

Por tanto usaremos un tercer paso para ello.

- El tercer paso será  ‘Paso Docker login’ usando la action login-action en su versión 4.
 Según la documentación del Market place se necesitan para esta acción los parámetros registry, username y password, ya definidos en variablees proporcionadas por git.

No me tengo que crear ninguna variable, ya que GITHUB_TOKEN lo proporcionqa ya GitHubActions y es un token temporal que se genera. 


- cuarto paso: “Paso setup Docker buildx”. La documntacion recomienda usar la action setup-buildx que permite construir imágenes multiplataforma.

Se usará la action de docker  ”setup-buildx-action” en su version 4

- quinto paso: ‘Paso buidl and push docker image”. Para publicar en el registry y hacer push. Se usará la action de docker “build-push-action” en su versión 7.

En este paso se necesitan 3 parámetros , entre ellos “ working-directory “  para que el workflow solo afecte a la parte del proyecto en el directorio hangman-front (./hangman-front). 
Los otros prámetros son push a true, para que compile la imagen, y el contexto, que es la ruta donde  queremos que haga la build.
 OJO!, en la etiqueta tags, tengo que incluir “ghcr.io” por delante.

Según la documentación es conveninete que haya permisos de lectura y escrituta en el repositorio, por lo que incluyo un apartado nuevo llamado ‘permissions’ (debajo de on)
Tras subir los cambios y publicar la rama, hago una pullrequest y  el merge con main. Finalmente haré git pull.

En Github, en Actions elegiré mi workflow cd-entrega continua y lo ejecutaré manualmente. 
