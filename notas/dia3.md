Para qué usamos un contenedor?

Para tener un entorno aislado donde ejecutar procesos.

Tipos de procesos: 
    - demonio, servicio     Se ejecutan infinitamente
        nginx
        apache wp
        mariadb
    - comando, script       Se ejecutan durante un periodo de tiempo finito
        backup
        etl
        playbook de ansible
        script de terraform 

---

# Objetos de Kubernetes - WORKLOAD

## Pod

Conjunto de contenedores:
- Comparten configuración de RED y dirección IP. Entre ellos se pueden hablar como "localhost"
- Se despliegan en el mismo host
- Pueden compartir volumenes locales
- Escalan juntos, a la vez.

Que tipos de procesos pueden ejecutar los contenedores de un POD? demonio, servicio

    Kubernetes en todo momento monitoriza el estado de los contenedores de todos los pods del cluster.
    Si un contenedor de un pod no está en funcionamiento, qué hace kubernetes? SE VUELVE LOCO !!!!
    Salen las alarmas, llama a los bomberos ! Lo flipa !

    Un pod, puede estar o no:
    - Inicializado
    - Listo

    Kubernetes nos permite definir chequeos para determinar cuando un pod no está en un estado saludable!
    Por defecto, cuando considera Kubernetes que un POD no se encuentra en estado saludable?
    Cuando el proceso 1 de alguno de los contenedores que tiene dentro no está corriendo. Nos vale esto? NO
    Ese es el comportamiento por defecto. Eso no nos vale. Por qué?
    Qué un proceso esté corriendo no significa que esté en funcionamiento
    Por ejemplo, para apache o nginx... cúal sería una buena prueba: Llamar al puerto http/https y que devuelva un 200

    Por ejemplo, cómo se sabe si un mariab está en funcionamiento? Interntar conectarme como administrador y ver que el motor BBDD está arriba 
    Y si estoy haciendo un backup o un procedimiento de recovery en la BBDD... va a contestar una query? NO 
        BBDD -> ADMINISTRACION
    Por ejemplo, cómo se sabe si un mariab está lista para prestar servicio? Intentar hacer hacer una conexión a la BBDD con un usuario y tirar una query.

    Startup Probes      Si falla, lo reinicia
    Lifeness Probes     Si falla, lo reinicia
    Readyness Probes    Si falla, hará otra cosa que luego os cuento!

Cuántos PODs vamos a crear en un cluster de Kubernetes? Ninguno: CORRECTA !
Nosotros no vamos a crear ningñun POD en kubernetes. PODRIAMOS, si, pero no lo hacemos.

Quién queremos que haga eso? Kubernetes

En lugar de crear pods, nosotros definimos Plantillas de PODS.

## Plantillas de pods

### Deployment

Plantilla de POD + número de replcias

### Statefulset

Plantilla de POD + Plantilla de Petición de volumen persistente + número de replicas

### Daemonset

Plantilla de POD, Kubernetes crea una instancia de ese pod en cada nodo del cluster

## Job

Conjunto de contenedores:
- Se despliegan en el mismo host
- Pueden compartir volumenes locales
- Se ejecutan secuencialmente, cuando uno acaba, otro empieza... hasta que todos han acabado

Que tipos de procesos pueden ejecutar los contenedores de un POD? scripts, comandos

    Kubernetes en todo momento monitoriza el estado de los contenedores de todos los jobs del cluster.
    Si un contenedor de un job no acaba, qué hace kubernetes? SE VUELVE LOCO !!!!
    Va a creer que el proceso se ha quedao colgao... Y lo reiniciará

Cuántos JOBs vamos a crear en un cluster de Kubernetes? Ninguno: CORRECTA !
Nosotros no vamos a crear ningún JOB en kubernetes. PODRIAMOS, si, pero no lo hacemos.

Quién queremos que haga eso? Kubernetes

En lugar de crear jobs, nosotros definimos Plantillas de JOBS.

## Plantillas de jobs: CronJob

Plantilla de Job + cron


---

En nuestro contanedor apache de WP tenemos 7 procesos en ejecución:
- 1 maestro
- 6 workers
 

# Objetos de Kubernetes - COMUNICACIONES
    
        Balanceador de carga: 192.168.1.100                  DNS               Cliente: 192.168.1.126
        |    192.168.1.100:80~>                               |                    |
        |         192.168.0.101:30080 , 192.168.0.103:30080   |  app1.prod.es      |  app1.prod.es
        |         192.168.1.102:30080                         |  192.168.1.100     |
-------------------------------------------------------------------------------------- Red de mi empresa
| [Cluster de Kubernetes]                       
|                                               
||==192.168.0.101-- Maquina 1
||                      NETFILTER:
||                        IPS de balanceo de carga base de datos      -> 10.1.0.1:33306 -> 10.0.0.4:3306
||                        IPS de balanceo de carga wp app1            -> 10.1.0.2:80    -> 10.0.0.1:80 | 10.0.0.3:80
||                        IPS de balanceo de carga ingresscontroller  -> 10.1.0.3:80    -> 10.0.0.5:80
||                        192.168.0.101:30080 ~> ingresscontroller:80
||             CRIO
||--10.0.0.1-- POD 1: Wordpress - Apache
||                      basedatoswp:33306
||--10.0.0.5-- POD 4: ProxyReverso-IngressController - Nginx
||                          app1.prod.es -> wpapp1              <<<<< INGRESS
||                          app2.prod.es -> wpapp2
||
||==192.168.0.102-- Maquina 2 
||                      NETFILTER:
||                        IPS de balanceo de carga base de datos      -> 10.1.0.1:33306 -> 10.0.0.4:3306
||                        IPS de balanceo de carga wp app1            -> 10.1.0.2:80    -> 10.0.0.1:80 | 10.0.0.3:80
||                        IPS de balanceo de carga ingresscontroller  -> 10.1.0.3:80    -> 10.0.0.5:80
||                        192.168.0.102:30080 ~> ingresscontroller:80
||             CRIO
||--10.0.0.4-- POD 2: Base de datos - Mariadb
||
||==192.168.0.103-- Maquina 3
||                      NETFILTER:
||                        IPS de balanceo de carga base de datos      -> 10.1.0.1:33306 -> 10.0.0.4:3306
||                        IPS de balanceo de carga wp app1            -> 10.1.0.2:80    -> 10.0.0.1:80 | 10.0.0.3:80
||                        IPS de balanceo de carga ingresscontroller  -> 10.1.0.3:80    -> 10.0.0.5:80
||.                       192.168.0.103:30080 ~> ingresscontroller:80
||             CRIO
||--10.0.0.3-- POD 3: Wordpress - Apache
||
||--DNS
        basedatoswp         -> 10.1.0.1
        wpapp1              -> 10.1.0.2
        ingresscontroller   -> 10.1.0.3
        

# Service

## ClusterIP
    Me permite pedir a kubernetes:
    - Una IP de balanceo de carga (que será fija) que gestione el propio kubernetes para 
        que siempre apunte a los pods donde corra un determinado tipo de proceso: WP
    - Una entrada en el dns de kubernetes que apunte a la IP de balanceo

## NodePort
    Es un servicio ClusterIP + Exposición de puertos en TODAS las maquinas del cluster que apunta al
    nombre DNS del balanceador del servicio

## LoadBalancer
    Es un servicio NodePort + Configuración automática de un Balanceador de carga externo al cluster.
    Para lo cuál necesito un balanceador de carga externo COMPATIBLE CON KUBERNETES !
    
    Si monto un cluster de Kubernetes en un cloud: AWS, AZURE, IBM CLOUD, los clouds me "dan" (previo paso por caja)
    un balanceador compatible con kubernetes
    
    Si monto mi propio cluster de kubernetes "ON PREMISES", solo hay 1 balanceador de carga compatible con Kubernetes:
    METALLB

## Ingress
    
    Regla de redirección para el ingresscontroller (proxy reverso)
    
## Router (ESPECIFICO DE OPENSHIFT)
    
    Ingress + Gestión automatizada de el DNS externo

## NetworkPolicy
    
    Es una regla de firwall que podemos aplciar en el cluster sobre la red virtual para controlar quién puede acceder 
    a los puertos de un pod (entrada al pod - ingress) y a donde puede atacar un pod (reglas de salida - egress)
    
    NO SE USA CASI ! Porque es una LOCURA CONFIGURARLO
--- 
Resumen:

Cuando quiero montar servicios internos al cluster, usamos servicios de tipo: CLUSTERIP
Cuando quiero montar servicios para uso externo, usamos servicios de tipo : NODE PORT o LOAD BALANCER

En un cluster típico de Kubernetes, cuántos servicios creeis que tendré de cada tipo?
    
    CLUSTERIP           todos menos 1
    NODEPORT             0
    LOADBALANCER         1

Voy a montar un cluster de kubernetes por servicio público? NO
Y si quiero ofrecer 10 sitios web distintos? 1

Qué servicio expongo: INGRESS CONTROLLER : PROXY REVERSO


Usuario 1  192.168.1.191                            
    v
    sitio web ----> RED ----> PROXY 192.168.1.100 --------------------> Sitio web
    ^                           Aquí puedo poner reglas de acceso
Usuario 2  192.168.1.78

Usuario 1
   v
 sitioweb1-----> RED ----> PROXY ------> ROUTER ------------------> PROXY REVERSO --->  sitio web1 192.168.1.109
                                                                                  --->  sitio web2
         
 sitioweb3                                                                        --->  sitio web3
Usuario 2                                                               sitioweb1 -> 192.168.1.109
                                                                        sitioweb3 -> 192.168.1.120

sitioweb1 y sitioweb3 : 53.9.7.68


NETFILTER > Componente del KERNEL DE LINUX  Que se encarga de la gesitón de TODOS los paquetes de red que pasan por la máquina
  ^
IPTABLES


Servicios internos CLUSTERIP en un cluster de kubernetes típico ? TROPECIENTOS MIL !

WAS, Weblogic, JBOSS ... están extintos !       J2EE JEE ~> JakartaEnterpriseEdition ~> Clase empresarial
    TOMCAT !                                                                            Clase WEB
    
Las aplicaciones gordas demostrarón ser una ruina gigante: YA NO MONTAMOS MONOLITOS. Ya no usamos EJBs

Hoy en día la arquitectura de moda es: MICROSERVICIOS !

Tengo en mi cluster 600 Tomcat en funcionamiento - 6000 tomcats

Como quiero las comunicaciones entre esos tomcats? http(s)

http + TLS

TLS 1: Donde quién presenta credenciales? El servidor (certificado = clave publica del servidor + firma de una Entidad certificadora)
                                                        clave privada
TLS 2: Los 2 van a presentar credenciales (certificados)

Qué me ofrece HTTPS? De qué me protege? Me ayuda a frustrar esos ataques:
- Man in the middle. Encriptado
- Suplantación de identidad

Estar generando 6000 certificados , con sus correspondientes claves publica y privada cada 2 meses y configurar 6000 tomcats

POD 1                   POD 2
Tomcat 1                Tomcat 2
   v                       ^
Envoy 1   >>>>>>>>>>>>  Envoy 2 Contenedor de tipo SIDECAR !

---

# OBJETOS PARA CONTROLAR VOLUMENES

Para qué sirven los volumenes?
- Persistencia tras el borrado de un contenedor
- Compartir datos entre contenedores
- Inyectar ficheros/directorios al contenedor

En Kubernetes los volumenes se definen a nivel de POD
Posteriormente, los contendores de un POD podrán usar o no esos volumenes.

POD WP
    Contenedores:
        Contenedor apache
            Volumen de almacenamiento del log: /var/log/apache
                                                    v
                                                access_log
                                                    ^
            Volumen de almacenamiento del log: /datos
        Contenedor filebeat
    Volumenes:
        Volumen de almacenamiento del log: DONDE TENDRÁ LA PERSISTENCIA REAL !
            Aquí solo definimos el tipo, si el volumen es NO PERSISTENTE !
            Por qué?
                Podría decir aquí : El volumen está en un NFS : nfs:///mynfssever/datos/mariadb: NO SE HACE NUNCA Y SE PUEDE !
            Para usar un volumen persistente: PETICION DE VOLUMEN PERSISTENTE

Vamos a clasificar los volumenes en 2 grandes tipos:
    - Volumenes persistentes        |   Volumenes para tener persistencia
    - Volumenes no persistentes     |   Volumenes para otros menesteres

## Hay muchos tipos de volumenes que podemos usar:

- Volumenes no persistentes     |   Volumenes para otros menesteres

                                                            CASO DE USO   
    - emptyDir      Crea una carpeta vacia en el host       Compartir info entre contenedores de un pod
    - configMap                                             Inyectar ficheros o directorios en un contenedor
    - secret                                                Inyectar ficheros o directorios en un contenedor
    - hostPath                                              Inyectar ficheros del host en un contenedor. CASOS RAROS DE USO
                                                                Si quiero que un contenedor accede a unas 
                                                                carpetas muy concretas del host
                                                                Soy un app de monitorización que quiero acceder a /proc

- Volumenes persistentes        |   Volumenes para tener persistencia
    - nfs
    - cabina
    - iscsi
    - bucket s3 amazon
    - disk azure
    - volumen vmware vsphere

Pregunta !!!! Dónde definiré el tipo de volumen que quiero usar?

PETICION DE VOLUMEN PERSISTENTE : PersistenVolumeClaim pvc ESTO LO RELLENA EL TIO DE LA APLICACION!
    - Espacio de almacenamiento: 100Gbs
    - Velocidad: NORMALITO !!!
    - Replicación x3
    - Encripción !

Yo, que soy el Admin del cluster... leo eso y preparo la FACTURA !!!!! 800€ !
1- Y crearé ese volumen donde sea ... donde a mi me interese... Donde haya mejores ofertas de BF!
                                        AWS
                                        CABINA
                                        iscsi
2- Tengo que informar a Kubernetes de esos datos !
VOLUMEN PERSISTENTE: PersistentVolume pv ESTO LO RELLENA EL ADMINISTRADOR DEL CLUSTER !
En él se detalla la información real del volumen... dónde tiene su persistencia.
...

POD MARIADB        <app>            PVC MARIADB     <kubernetes>      PV MARIADB
    Contenedor MariaDB    
            /var/mariadb/data       Quiero 3 Gbs                      Bucket S3 AWS : 192844828264 region eu-west-1
    Volumen:  ^                     Normalito                           Tiene 5Gbs
     PVC MARIADB                    Redundancia x3                      Redundancia x3
                                                                        De velocidad Normalita

# Por qué tengo un objeto para el PV y otro para el PVC? Separación de responsabilidades entre NEGOCIO (app) y ADMINISTRACION

# Por qué tengo un objeto POD y por separado un PVC? Qué pasa si borro el pod en un momento dado?

Tengo el pod mariadb creado desde la version 5.7.3 de MariaDB 
Me cargo ese pod y creo el pod mariadb desde la version 5.7.4 de MariaDB. Quiero pedir OTRO VOLUMEN? NO... quiero el mismo de antes
Yo solo he querido hacer 1 peticioón de volumen... que reusaré en varios pods.

Cada vez que un tio de negocio pida una pvc (un volumen) necesito ahí al admin del cluster, creando el volumen? NO

En Kubernetes podemos montar un PROVISIONER: Es un programa que en automático:

- Creará un volumen en el backend adecuado (AWS, Cabina, NFS)
- Dara de alta esa info en kubernetes (creando un objeto PV en kubernetes)

1º RESPONSABILIDAD DEL ADMINISTRADOR DEL CLUSTER será configurar un PROVISIONADOR adecuado.
    Para ese provisionado, el administrador debe definir un nuevo tipo de objeto en kubernetes: STORAGE CLASS
        STORAGE CLASS: TIPO DE ALMACENAMIENTO ATENDIDO POR UN DETERMINADO PROVISIONADOR
                        rapidito-x3 ----> Al provisionador de amazon este tengo aquí configurado
    Si uso Openshift, ya me viene instalado de antemano un montón de ellos. 

PROBLEMA DE ESTO?
- Que al tio de la app se le vaya la cabeza ! que se flipe!... y como no hay nadie detrás... me la lia parda!

PARA RESOLVER ESTO:
Kubernetes nos da 2 tipos de objetos adicionales que operan a nivel de NAMESPACE: RESOURCE QUOTAS | LIMIT RANGES

# RESOURCE QUOTA:
Limitación agregada de recursos que se pueden usar dentro de un namespace:
    En el namespace A puedes:
        - Crear 5 pods como mucho
        - Usar en total 10 cores
        - Usar en total 20 Gbs de RAM
        - Realizar como mucho 5 pvc
        - Pedir como mucho 200 Gbs de almacenamiento rapidito

# LIMIT RANGE
Limitación individual de recursos que se pueden usar dentro de un namespace:
    En el namespace A, cada pod:
        - Puede usar como mucho 2 cores
        - Puede usar como mucho 6 gbs de RAM
        - Puede usar como mucho 10 Gbs de almacenamiento

Maquinas de 8 cores:
Llega un flipao y me pide un pod con 4 cores... Que pasa? Me acaba de joder una maquina !

---

Objetos de Kubernetes:

- Namespace (que en openshift se llaman PROJECT)
    Espacio lógico en el que no se pueden repetir los nombres (deben ser únicos)
    Los usamos para separar:
    - proyectos en un cluster
    - entornos: Desarrollo, produccion, pruebas
    La gracia es que el acceso (permisos) a kubernetes se otorga a nivel de NAMESPACE
Workload:
- Pods <- Deployments, Statefulsets, Daemonsets 
- Jobs <- CronJobs
Comunicaciones:
- Services
    - ClusterIP
    - NodePort
    - LoadBalancer
- Ingress -> IngressController (Proxies reversos) ~> Kubernetes
- Router
- NetworkPolicy
Volumenes:
- Pvc
- Pv
- StorageClass
Limitaciones:
- ResourceQuota
- LimitRange
---

Openshift

Es una distribución de Kubernetes

---
REDHAT 
    SELINUX: Security Enhanced for Linux
    
    Definimos politicas
    Selinux tiene 3 modos: Deshabilitado, permisivo, forzado