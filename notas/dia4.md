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
    


