####################################################################################################
#       CARREFOUR
####################################################################################################

Supermercado:                                                                   Cluster de Kubernetes
    Carnicería                                                                  Servicio
        Cámara frigorifica                                                      Volumen de almacenamiento en frío
        Pedir turno, coger número... pantallita-> 37                            Balanceador de carga
        Puesto de trabajo                                                       Máquina 1
            Nevera mostrador                                                    Volumen de almacenamiento en caliente
            Tabla                                                               RAM
            Cuchillos                                                           CPU
            Báscula
            Papel
            - Carnicero1                                                        Pod: 1 Contenedor
        Puesto de trabajo                                                       Máquina 2
            - Carnicero2                                                        Pod: 1 Contenedor
    Pescadería                                                                  Servicio
    Frutería
    Panadería
    Cajas
        Cola única -> Pantalla -> Caja 14                                       Balanceador de carga
        Caja 14 - Puesto de trabajo
            Caja, TPV
            - Cajero(a)
    Carteles 
        - Carnicería                                                            DNS
        - Frutería
    Puerta de los clientes del supermercado                                     Proxy reverso - IngressController
    Puerta de emergencia
    Puerta de mercancias
    Gerente !                                                                   Kubernetes
        Buscar otra persona que tenga un determinado
            PERFIL, CONOCIMIENTOS                                               IMAGEN DE CONTENEDOR
    Mozos de almacen... que deben cargar palets y van de 2 en 2                 POD: 
        Mozo 1                                                                      Contenedor
        Mozo 2                                                                      Contenedor
Productos                                                                       Datos !

Si monto la tienda de ultramarinos de la esquina del tio PERE !
---


Hospital:                                                                       Cluster de Kubernetes
    Dermatología                                                                  Servicio
        Pedir turno, coger número... pantallita-> 37                            Balanceador de carga
        Puesto de trabajo                                                       Máquina 1
            - Dermatologo                                                        Contenedor
        Puesto de trabajo                                                       Máquina 2
            - Carnicero2                                                        Contenedor
    Traumatología                                                                  Servicio
    Urgencias
    Triaje
        Cola única -> Pantalla -> Caja 14                                       Balanceador de carga
        Caja 14 - Puesto de trabajo
            Caja, TPV
            - Cajero(a)
    Carteles 
        - Carnicería                                                            DNS
        - Frutería
    Puerta de los pacientes                                                     Proxy reverso - IngressController
    Puerta de emergencias
    Puerta de medicos
    Gerente !                                                                   Kubernetes
        Buscar otra persona que tenga un determinado
            PERFIL, CONOCIMIENTOS                                               IMAGEN DE CONTENEDOR
Productos                                                                       Datos !


Cluster de Kubernetes
    Máquina CP1
        gestor de contenedores: CRIO
            coreDNS
            etcd
            kube-api
        kubelet
    Máquina CP2
        gestor de contenedores: CRIO
            coreDNS
            etcd
            kube-scheduller
        kubelet
    Máquina CP3
        gestor de contenedores: CRIO
            etcd
            kube-controller
        kubelet
    ...
    Máquina 1
        gestor de contenedores: CRIO
        kubelet
    Máquina 2
        gestor de contenedores: CRIO
        kubelet
    Máquina n
        gestor de contenedores: CRIO
        kubelet
    
Dentro de Kubernetes encontramos: -> CONTROL PLANE de Kubernetes
    Un servidor de DNS                                                          coreDNS
    Un servidor de BBDD                                                         etcd
    Un programa que determina dónde se ubica un contenedor para su ejecución    kube-scheduller
    Un programa que gestiona todas las peticiones del cluster                   kube-api
    Un programa que controla el cluster                                         kube-controller
    
    
# En Kubernetes

Nosotros damos toda la configuración mediante archivos YAML, que usan una sintaxis DECLARATIVA, no IMPERATIVO

Hay muchas cosas que configuramos en Kubernetes: OBJETOS

## Objetos de Kubernetes

### POD 

Conjunto de contenedores que...
- Comparten configuración de red -> IP... y por ende se comunicacn entre si mediante la palabra: localhost
- Se despliegan en el mismo host
- Escalan de la misma manera
- Pueden compartir volumenes locales

---


La silla está debajo de la ventana.         Enunciativa afirmativa
La silla NO está debajo de la ventana.      Enunciativa negativa
Está La silla debajo de la ventana?         Interrogativa
Pon la silla debajo de la ventana.          Imperativa
Una silla debe estar debajo de la ventana.  Desiderativa ~ Declarativa

Ordenador!   DEL PUÑETERO GABACHO !!! L'ordinateur Ordena datos? NO de hecho lo hace como el puto culo !
   ^^^
Computadoras

HOY EN DIA ODIAMOS LOS LENGUAJES IMPERATIVOS. Son una mierda !!!

SCRIPT: Imperativo 
    IF algo debajo de la ventana:
        quitalo!
    IF not silla: 
        vete al ikea!
    Felipe, pon la silla debajo de la ventana !
    
SCRIPT: Declarativo : SON GUAYS !!!!!!
    Una silla debe estar debajo de la ventana. PUNTO PELOTA !

Ansible, Puppet, Chef, Terraform, Kubernetes, openshift


## Ejemplo 1

Wordpress... Cuantos contenedores necesito al menos? 2

                                                IIIIIIIIIII
    POD UNICO:                              POD BBDD    +   POD WP
        1 - BBDD                             - BBDD          - WP
        2 - Apache + WP

Conjunto de contenedores que...
x Comparten configuración de red -> IP... y por ende se comunican entre si mediante la palabra: localhost
x Se despliegan en el mismo host
x Escalan de la misma manera
x Pueden compartir volumenes locales


## Ejemplo 2

Cluster de servidores de Apache... Por ejemplo los de nuestro WP

Esos apaches van a generar unos access_log ---> ElasticSearch < Kibana


Apache1
    VVV
    access_log  RAM (100Kbs) 2 archivos rotados
    ^^^
Filebeat | Fluentd >>>>>

Apache2
    VVV
    access_log
    ^^^
Filebeat | Fluentd >>>>>    Cluster Kafka   <<<< Logstash >>> Cluster ElasticSearch <<<< Kibana

Apache3
    VVV
    access_log
    ^^^
Filebeat | Fluentd >>>>>

# OPCION 1

Si el apache guarda el access_log en el HDD del host donde se encuentra, 
el filebeat debe estar en el mismo host

# OPCION 2

Si el apache guarda el access_log en un almacenamiento externo, 
el filebeat debe estar donde le dé la gana (en una única máquina leyendo todos los logs)

10 apaches... esupiendo logs a un almacenamiento externo (RED???)


POD:
    Contenedor: Apache              Servicio
    Contenedor: filebeat (SIDECAR)  Demonio