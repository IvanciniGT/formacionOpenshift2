# Objetos Kubernetes

- Namespace[ns]     Espacio lógico en el cluster en el que los nombres no pueden repetirse.
                    Los permisos/limitaciones se asignan a nivel de namespace

## Workload (NAMESPACED)

- Pod - Tiene contenedores que deben ejecutar servicios/demonios (paralelo)         
- Job - Tiene contenedores que deben ejecutar scripts/comandos.  (secuencialmente)
- Deployments - Plantilla de pod + número de réplicas
- Statefulset - Plantilla de pod + Plantila de pvc + número de réplicas
- Daemonset   - Plantilla de pod (genera pod en cada nodo del cluster)
- hpa - HorizontalPodAutoscaler Nos sirve para indicar a Kubernetes cómo queremos escalar un deployment
                                Quiero tener entre 5 y 10 nginx
                                Cuando la cpu esté al 40% PA'RRIBA !

# Comunicaciones (NAMESPACED)

- Service[svc]
    - ClusterIP         Entrada en DNS + IP de balanceo
    - NodePort          ClusterIP + puerto expuesto en cada nodo apuntando a la entrada en DNS
    - LoadBalancer      NodePort + Configuración automatizada del balanceador externo
- Ingress               Regla para un IngressController (proxy reverso)
- NetworkPolicies

# Almacenamiento

- PersistentVolumeClaim[pvc]        (NAMESPACED) Petición de un volumen persistente
- PersistentVolume[pv]              Datos identificativos de un volumen alojado en un emplazamiento externo al cluster (cloud, cabina, nfs)
- StorageClass                      Tipo de volumen que tiene asociado un PROVISIONADOR AUTOMATIZADO

# Limitaciones (NAMESPACED)

- ResourceQuota         Limitar total de recursos
- LimitRange            Limitaciones sobre cada objeto concreto

# Objetos extendidos que amplian funcionalidad del cluster de Kubernetes 
CRD
    Certificado SSL
    Peticion de Certificado SSL
    Proveedor de Certificados SSL

De cara a trabajar contra kubernetes, tenemos un cliente de linea de comandos, similar al que teníamos con docker:
$ kubectl
$ oc

kubectl <verbo> [TIPO_OBJETO] <args>

verbos: get         Un equivalente al list de docker
        describe    Detalle de un objeto. Equivalente al inspect de docker
        delete
    
        apply       Crear / modificar un recurso, a través de un fichero


argumentos:
    -n --namespace  Filtrar a un namespace
                    Por defecto, si no filtramos, todo cluster tiene un namespace "default"
    --all-namespaces
    
    
                        PROGRAMAS
                         vvv
                         Balanceo - SERVICE             VirtualService
     vvv 0%                                vvv 100%
    Pasarela de Pago v1 - POD           Pasarela de Pago v2 - POD
    

## Objetos para configurar PodTemplates

- Deployment            Plantilla pod + numero de replicas (inicial)
- Statefulset           Plantilla pod + Plantilla de PVC + numero de replicas (inicial)
- Daemonset             Nosotros lo usamos poco

Cuando creamos un deployment, todos los pods que se creen (replicas) usarán los mismos volumenes
Cuando creamos un statefulset, cada pods que se cree (replicas) usarán su propio volumen, obtenido desde su propia petición de volumen

# Quiero desplegar un WP:

PLANTILLA POD WP            ---> Muchos pods
- Contenedor Apache+php+wp

PLANTILLA POD BBDD          ---> Muchos (aunque menos) pods
- Contenedor BBDD: Mariadb

                                                                SI              NO 
Los pods del WP, quiero que compartan volumen o no?             √                           DEPLOYMENT
Los pods de la BBDD, quiero que compartan volumen o no?                         √           STATEFULSET

Cuando la respuesta sea, que quiero que todos los pods compartan volumen: Usar un Deployment
Cuando la respuesta sea, que quiero que cada pod tenga su propio volumen: Usar un Statefulset

BBDD Mariadb

Standalone  - Ella sola corriendo (1 nodo)
---
Replicación - Master (Mirrors)                  Activo - Cuasi[Pasivo]

    MariaDB MASTER                          MariaDB Replica
        Donde guarda los datos?                 Donde guarda los datos?
        En su propio volumen (carpetas)         En su propio volumen

Mariadb Galera - Cluster activo/activo. Como poco 3 (esto es un estandar en los sistemas de almacenamiento) - BrainSplit

        MariaDB1        Dato1   Dato3        
        MariaDB2        Dato1   Dato2               ENVOY
        MariaDB3        Dato2   Dato3                   p1 p2 p3 p4 p5 p6 p7 p8 p9
    Tengo una mejora del rendimiento de un 50%

        MariaDB2                ENVOY   p1 p2 p3 p4 p5 p6 p7

    ISTIO LINKERD
---
                                        Apache 1
                                            WP          1º Tener el código de la app
Cliente1             BC                                 2º Contenido dinámico
Cliente2                                Apache 2
                                            WP          1º Tener el código de la app
                                                        2º Contenido dinámico
----

Montaremos múltiples replicas de un pod cuando queremos  ?  Escalabilidad? SI        Alta disponibilidad? NO
                                                            Cluster Activo-Activo    Cluster Activo-Pasivo

Pregunta. En un cluster de kubernetes, con 1 solo pod (replica) tengo un cluster activo/pasivo? SI porque Kubernetes el pasivo, 
                                                                                                lo crearía bajo demanda cuando sea necesario
                                                                                                
Los datos van fuera del cluster! En un sistema de almacenamiento externo con redundancia y alta disponibilidad

Me tengo que preocupar por los procesos que usan los datos.. no por los datos!

Mariadb se cae !
Levantar el mariadb en otro maquina, apuntando a los mismos datos !


# Objeto Node de Kubernetes:  Un entorno en el que estamos ejecutando kubelet

# Objeto Machine de Openshift: Una máquina física, esté o no ejecutando kubelet

MachineSet: Plantilla de Maquinas
MachineAutoscaler: Generar máquinas físicas bajo demanda ! -> Node del cluster, activandoles un kubelet y registrandolas en el cluster

TERRAFORM ! Automatizar la gestión de infraestructuras en un cloud


minikube    Cluster de kubernetes local que requiere muy pocos recursos 


----

1 Gibibyte = 1024 Mebibytes = 1024 Kibibytes = 1024 bytes

1 Gigabyte = 1000 Megabytes = 1000 Kilobytes = 1000 bytes
