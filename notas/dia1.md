# Modelos de instalación de software

## Modelo clásico

        App1 + App2 + App3              Problemas:
    ----------------------------            - Incompatibilidades en librerías / dependencias
        Sistema Operativo                   - App1 (100% CPU) --> OFFLINE.. App2 + App3 van detrás
    ----------------------------            - Seguridad
            HIERRO

## Modelo basado en Máquinas Virtuales

      App1    |  App2 + App3            Problemas:
    ----------------------------            - Desperdicio de recursos
      SO 1    |      SO 2                   - Merma en el rendimiento
    ----------------------------            - Complejidad de configuración/mnto
      MV 1    |      MV 2
    ----------------------------
     Hipervisor: Citrix, VMWare
     HyperV, KVM, VirtualBox
    ----------------------------
        Sistema Operativo 
    ----------------------------
      HIERRO (8 cores, 32 Gbs) 

## Modelo basado en Contenedores

      App1    |  App2 + App3
    ----------------------------
      C 1     |      C 2
    ----------------------------
     Gestor de contenedores:
     Docker + Podman + CRIO + ContainerD
    ----------------------------
      Sistema Operativo Linux
    ----------------------------
      HIERRO (8 cores, 32 Gbs) 

# Contenedor

> Entorno aislado donde ejecutar procesos en un SO Linux (principalmente)
>
> Aislado? 
> - Su propia configuración de red -> Su propia dirección IP
> - Sus propias variables de entorno
> - Su propio sistema de archivos
> - Puede tener limitación de recursos del Hardware

Los contenedores se crean desde Imágenes de contenedor.

## Imágenes de contenedor

Un triste archivo comprimido (tar) que contiene:
- Un programa YA INSTALADO POR ALGUIEN y CONFIGURADO
- Dependencias que pueda tener ese software
- Otros programas que puedan ser de utilidad

+ Una serie de configuraciones adicionales que aplicarán cuando creemos un contenedor desde esa imagen.

Las imágenes de contenedor las encontramos en REGISTRIES de REPOSITORIOS de imágenes de contenedor:
- Docker hub
- Quay.io       Registry de Redhat

Las imágenes de contenedor se identifican mediante: REGISTRY/REPO:TAG
Habitualmente, el repo no se suministra... y se us el que haya configurado localmente a nivel del gestor concreto de contenedores que esté utilizando

Si quiero desplegar un nginx:
    REPO: nginx
    TAG:  Identifica una imagen concreta dentro del repo.
            Habitualmente, como parte de ese tag, encontramos la versión de software instalada
            - latest        NO... ni de coña... por qué? No tengo ni idea de qué versión me estoy instalado
            - 1.23.2        Esta no, ya que si salen arreglos de bugs, los quiero tener
            - 1.23          ESTA ES GUAY PARA PROD
            - 1             Esta no. Ni quiero funcionalidades nuevas,ni las necesito
            Si no suministro un tag a la hora de referirme a una imagen de contenedor:
            Por defecto, el gestor de contenedores busca el tag: 'latest'

## Nota: Versionado de software
    1.23.2              Cuándo se incrementa?
        1 ?? MAYOR      Cambio de diseño (arquitectura) OJO AQUÍ PUEDE NO RESPECTARSE
                        RETRO-COMPATIBILIDAD
        23 ? MINOR      Aumentos de funcionalidad
        2 ?? MICRO      Arreglo de bugs


## Qué era UNIX® ?

Un sistema operativo creado por los Lab. Bell de AT&T

## Qué es UNIX® ?

Es una colección de estándares (2: POSIX + SUS) que nos explican cómo poder montar un SO.

### SO Certificados UNIX

- IBM: AIX
- HP:  HP-UX
- Apple: MacOS
- Oracle: Solaris

### SO No certificados UNIX, pero que "supuestamente" siguen esos estándares

- GNU/Linux:
  - RHEL: Fedora, Oracle linux, CentOS
  - Debian: Ubuntu
  - Suse...
- BSD
- Android

## POSIX
/
    bin/    Binarios / comandos ejecutables
    opt/    Otro software
    var/    Datos de los programas
    home/   Usuarios
    root/   Usuario root
    tmp/    Temporal
    etc/    Configuraciones
    mnt/ 
    boot/
    proc/




Kubernetes

## Nota 1:

Quiero instalar SQL Server en mi máquina. Qué necesito?
(Ya tengo máquina, SO, y todo preparado)
1- Me bajo de la web de Microsoft el Instalador de SQL Server
2- Ejecuto el instalador + Configuraciones
3- Ejecuto

C:\Archivos de programa\SQLServer > ZIP >> email

## Nota 2 - Tipos de Software

- Sistema Operativo
- Aplicación: Videojuego, Word, Chrome  Pensada para interactuar con un ser humano a través de una UI
------------------------------------------------------- v Es contenedorizable
- Driver
- Demonio                               Programa que corre en 2º plano
  - Servicio    BBDD + Servidores Web   Programa que corre en 2º plano e interactúa con otros
                                        programas: Sockets(Puertos) + Colas + Shared Memory
- Comando:
- Script: Secuencia de llamadas a comandos
 
# Docker 

Docker tiene 2 partes:
- cliente   docker
- servidor  dockerd

Sintaxis cliente de docker:

$ docker [TIPO_DE_OBJETO] [VERBO] <args>

## TIPOS DE OBJETO EN DOCKER: IMAGE CONTAINER VOLUME, NETWORK

## VERBOS

Dependen del tipo de objeto: LIST RM INSPECT 
    CONTAINER: START; STOP; RESTART; CREATE; LOGS
    IMAGE:     PULL

**NOTA: Casi todos los comandos de docker tienen un alias

docker RUN
    image pull + container create + container start + container attach
    
# Interfaces de RED:

- loopback: 172.0.0.1       - localhost
- eth:      172.31.32.106   - red de amazon
 

## DEVOPS

No es un perfil....  es una CULTURA, es una filosofía!
La cultura de AUTOMATIZACION ! Automatizar qué? Todo lo que hay entre desarrollo y operación.
    
                Automatizable?                              Quién?
PLAN                x
CODE                x
BUILD               √           JAVA: maven, gradle, sbt.   Desarrollo
                                C#: msbuild dotnet nuget
                                JS:   npm yarn webpack
TEST                
    diseño          x
    pruebas         √           Selenium Appium Katalon     Desarrollo
                                Karate Cucumber Sonarqube   Q&A
                                JMeter Loadrunner SoapUI 
                                postman JUNIT
        Generamos un entorno de pre-producción, Q&A, test, de usar y tirar
                                docker, kubernetes, terraform, ansible
                                                            SysAdmin
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< Integración continúa
RELEASE     ----> Imagen de contenedor
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< Entrega continúa
DEPLOYMENT
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< Despliegue continuo
OPERATION
MONITOR

        Me falta un ORQUESTADOR ! Alguien que llame a todos esos automatismos:
            JENKINS, AZUREDEVOPS, TRAVISCI, GITLAB CI/CD, BAMBOO, TEAMCITY

### Integración continua CI
### Entrega continua            <<<<<<<
### Despliegue continua

OPERACION de un sistema:


## ENTORNO DE PRODUCCIÓN:

- ALTA DISPONIBILIDAD
    "Tratar" de garantizar un determinado tiempo de servicio, firmado contractualmente
        90% de tiempo de servicio: RUINA !   1 de cada 10 dias el sistema puede estar OFF           |   €
        99%         3,6 días al año el sistema OFF | PELUQUERIA DE LA ESQUINA y mi web de reservas  |   €€
        99,9%       8 horas al año                                                                  |   €€€€€
        99,99%      minutos al año                                                                  v   €€€€€€€€€€€€€€€€
            REDUNDANCIA !
                Cluster: 
                    Activo-Activo           -> Balanceador de carga
                    Activo-Pasivo
    "Tratar" de garantizar la NO PERDIDA de información
            REDUNDANCIA del dato. x3  RAID5
- ESCALABILIDAD
    Capacidad de ajustar la infra a las necesidaddes de cada momento
        App1  INTERNET !
        HORA n          100 usuarios
        HORA n+1    1000000 usuarios            ESCALABILIDAD HORIZONTAL ! MAS o MENOS MAQUINAS ! CLOUDs IBM
        HORA n+2      10000 usuarios
        HORA n+3   20000000 usuarios

KUBERNETES ! Google
Redhat -> Openshift
VMWare -> Tamzú + Bitnami!


docker exec minginx ls /


1.23.2
1.23.3

## Imágenes base de contenedor

Es un fichero comprimido que dentro tiene:
- La estructura de carpetas típica de un sistema POSIX
- 4 comandos en /bin:
    - ls, mkdir, touch, cat, sh, bash

Docker desktop para Mac: Docker desktop levanta una máquina virtual y dentro corre un kernel linux, sobre el que se ejecutan los contenedores
Docker desktop para Windows: 
    - wls1: Docker desktop levanta una máquina virtual hyperv y dentro corre un kernel linux, sobre el que se ejecutan los contenedores
    - wls2

## Redirección de puertos en contenedores

            RED DE AMAZON
    ------------------------------------------------------------------------------------------------
        |                                                                       | IP?
        172.31.32.106:8080 -> 172.17.0.2:80                                   MENCHU PC 
        |
    IvanPC--127.0.0.1--LOOPBACK
        |
        172.17.0.1
        |
        Red de docker -- 172.17.0.2:80 -- Contenedor nginx

## Sistema de archivos de un contenedor

HOST:
/
    bin/
        ls
    opt/
    var/
        lib/
            docker/
                containers/
                        ....
                            mariadb/
                                    var/
                                        mariadb/
                                            data        ficheros de la BBDD
                                            log         ficheros de log de la BBDD
                            mariadb2/
                                    var/
                                        mariadb/
                                            data        ficheros de la BBDD
                                            log         ficheros de log de la BBDD
                images/
                        ....
                            mariadb/      <<< Engañamos a los procesos que corren en el contenedor    
                                            haciéndoles creer que ESTA carpeta es el ROOT.
                                            ESTO NO ES TAN NOVEDOSO !   chroot
                                bin/
                                    ls
                                opt/
                                    mariadb/
                                        mariadb
                                var/
                                home/
                                root/
                                tmp/
                                etc/
                                    mariadb/mariadb.conf
                                mnt/ 
                                boot/
                                proc/
    home/
        ubuntu/datosBBDD1
              /mariadb.custom.conf
    root/
    tmp/
    etc/
    mnt/ 
    boot/
    proc/
    
El sistema de archivos de un contenedor está montado por la superposición de varias capas:
                                                            vvvvvv
- VOLUMEN           /home/ubuntu/datosBBDD1 ---> /var/mariadb/datos
- CAPA CONTENEDOR
- CAPA BASE / IMAGEN: Esta capa es INALTERABLE 

A los contenedores siempre les añadimos VOLUMENES. Sirven para:
- Dar persistencia tras el borrado de un contenedor a sus datos
- Inyectar ficheros/directorios desde el host hasta el contenedor
- Para compartir información entre contenedores distintos


nginx / httpd
      vvvv
    access_log  > RAM 2 rotados de 50Kbs
      ^^^^
fluentd / filebeat ----> ElasticSearch << Kibana