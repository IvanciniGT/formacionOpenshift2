# Contenedor

Entorno aislado donde ejecutar procesos dentro de un SO con kernel Linux

Aislado???
- Su propia configuración de red
- Su propio sistema de archivos
- Sus propias variables de entorno
- Podía tener limitaciones para acceder a los recursos del hierro

Los contenedores se crean desde Imágenes de contenedor

# Imagen de contenedor

Un triste fichero comprimido (tar) que contiene:
- El programa instalado y con una configuración de partida
- Sus dependencias y librarias requeridas
- Otro software que pueda ser de utilidad
Todo ello bajo un esquema de directorios compatible con POSIX.

Adicionalmente, en la imagen de contenedor se incluyen algunas configuraciones que aplicarán
a los contenedores que se generen desde esa imagen de contenedor

Las encontramos en: REGISTRIES DE REPOSITORIOS DE IMAGENES DE CONTENEDOR:
- Docker hub
- Quay.io       REDHAT

Las imagenes de contenedor se identifican mediante: RESTRY/REPO:TAG
REPO ~ Nombre del software instalado
TAG  ~ VERSION + información adicional (de otro software que se incluye)

Al crear un contenedor tenemos opción de:
- Inyectar volumenes
- Redirecciones de puertos.

# Volumenes ??? Para qué sirven?

- Persistencia de datos tras el borrado de un contenedor
- Compartor información entre varios contenedores
- Inyectar archivos/directorios (configuraciones, web, ... ) dentro de un contenedor

## Estructura del filesystem del contenedor

Se monta mediante la superposición de capas:

- VOLUMENES:           Puntos de montaje en el FS del contenedor (PATH) que apuntan a almacenamientos 
                       externos al contenedor
- CAPA DEL CONTANEDOR: Al borrar un contenedor, esta capa se pierde
- CAPA BASE / IMAGEN:  Es Inalterable

# La red en los contenedores

El contenedor tiene su propia configuraciónd de red....
- En Docker por defecto crea una red virtual local en la que pincha los contendores: INACCESIBLE desde fuera del host
  Para que externamente se pueda acceder a un servicio de un contenedor qué hacemos? Un NAT. Una redirección de puertos
- En kubernetes, el tema va por un lado TOTALMENTE DIFERENTE

# Para trabajar con contenedores necesitamos de GESTORES DE CONTENEDORES:

- Docker
- Podman < RHEL             Pod Manager
- Crio
- ContainerD

# Docker

Docker es un gestor de contenedores que usamos en entornos "locales"

Tiene una parte cliente y una parte servidor.

El cliente, el comando "docker" lo podemos usar:

$ docker [TIP_DE_OBJETO] [VERBO] <args>

TIPO_DE_OBJETO: image container volume network secret
VERBOS:
    Genericos:      create list inspect rm 
    Contenedores:   start stop restart logs attach detach
    Imagenes:       pull push

# Crear un contenedor:

$ docker container create \
    --name NOMBRE \
    -v RUTA_HOST:RUTA_CONTENEDOR \
    -p IP_HOST:PUERTO_HOST:PUERTO_CONTENEDOR (en caso de no suministrar IP_HOST se usa la máscara 0.0.0.0) \
    -e VARIABLE=VALOR \
    -e VARIABLE2=VALOR2 \
    imagen

# Entornos de producción

- Alta disponibilidad > Redundancia
- Escalabilidad       > Redundancia (cluster activo/activo)

En el mundo de las TIC, esto lo implementamos mediante clusters máquinas/procesos.
Clúster? GRUPO / CONJUNTO

Docker, el demonio de docker opera a nivel local, sobre una máquina.
Existe una herramienta que ofrece la gente que hace docker: DOCKER SWARM, que permite 
gestionar un cluster de contenedores alojado en varias máquinas. NO LO USA NADIE !

En su lugar TODO EL MUNDO usa Kubernetes para este fin. OFRECE MUCHA MAS FUNCIONALIDAD.

NOTA: Hay procesos en producción que ejecuto en entornos locales... y ahí puedo usar docker.


Cluster de Kubernetes <<<< Quiero tener un contenedor con MariaDB en el cluster
- Máquina 1
    CRIO
- Máquina 2
    CRIO
        Contenedor MariaDB4
            Tendrá su sistema de archivos: CAPA BASE + CAPA CONTENEDOR + VOLUMEN "/var/mariadb/data" -> RUTA EXTERNA
- Máquina N    
    CRIO

- Sistema de almacenamiento externo: NFS, CABINA, CLOUD
    Datos MARIADB 

---

Quiero montar una base de datos, usaré un contenedor para su ejecución.
Necesito partir de IMAGEN DE CONTENEDOR, que incluirá LA BBDD YA INSTALADA Y PRECONFIGURADA

Los desarrolladores, cuando crean software que vaya a ser contenedorizado, tienen ese hecho en cuenta.
Lo que hace es cierta configruación del software que están creando leerlo de variables de entorno.
Las imágenes, definen unas variables de entorno (con valores por defecto en algunos casos).


# Vamos a montar un software: WORDPRESS

IMAGEN: Apache - php < Wordpress
IMAGEN: BBDD (MariaDB)

docker container create   \
    --name wp_db \
    -p 172.17.0.1:3307:3306 \
    -e MARIADB_ROOT_PASSWORD=password \
    -e MARIADB_USER=usuario \
    -e MARIADB_PASSWORD=password \
    -e MARIADB_DATABASE=basedatos \
    -v /home/ubuntu/environment/datos/mariadb:/var/lib/mysql \
    mariadb:10.10-jammy

docker container create   \
    --name wp \
    -p 8080:80 \
    -e WORDPRESS_DB_HOST=172.17.0.1:3307 \
    -e WORDPRESS_DB_USER=usuario \
    -e WORDPRESS_DB_PASSWORD=password \
    -e WORDPRESS_DB_NAME=basedatos \
    -v /home/ubuntu/environment/datos/wp:/var/www/html \
    wordpress:php8.1-apache

---

# Cliente docker-compose

Me permite definir contenedores en ficheros YAML (multiples contenedores en un único fichero)

Lo invoco con el comando:
$ docker-compose <VERBO> <args>

VERBO:
    start       arrancarlos
    stop        pararlos
    up          create | update + start
    down        stop + delete
    restart

args:
    -d Modo detached (sin bloquear la consola ni mostrar logs por defecto)
    -f Fichero que contiene la especificación de contenedores. Por defecto, si no se suministra:
        - docker-compose.yaml
        - docker-compose.yml
