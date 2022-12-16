# Politicas de afinidad de PODs:

Cada pod podemos asignarle en paralelo 3 politicas de afinidad:
- Afinidad a nivel de Nodo      Required | Preferred
- Afinidad a nivel de Pod
- AntiAfinidad a nivel de Pod 
 
Cluster Nginx  | Tomcats App1


Pod 1
Pod 2
Pod 3
Pod 4
Pod 5

# Taints / tolerations


---

Soy desarrollador  y monto una web con php... js ... lo que sea
Para ponerla en producción en kubernetes que necesito hacer?

1º Generar mi propia imagen de contenedor partiendo de un nginx/apache en la que dentro pongo mi app
    Dockerfile
2º Subir esa imagen a un registry de imagenes de contenedor
3º Preparar todos los archivos de despliegue en kubernetes:
    Deployments
    PVCs
    Services
    Ingress
    ....
    
    
El software funcionando es la medida principal de progreso.

2 métodos


10 HITOs    Fecha 10-enero  RETRASO? -> REPLANIFICO EL HITO -> 20-Enero -> RETRASO !
    Funcionalidades:
        R1  -> T1 T4
        R2
        R17
        R23


SPRINT    Fecha 10-enero .... Entrego las funcionaldiades que SI ESTEN LISTAS 
    Funcionalidades:
        R1  -> T1 T4
        R2
        R17
            INSTALAR ! en producción !

SPRINT 2 - 20-enero
    R23

---

Proyecto (Namespace)
    ResourceQuota 2gb 2cores


MariaDB -> Plantilla 
    Despliegue
        Pod
    Service
    Peticion volumen ...

WP -> A mano
    Deployment
    Service
    Route
    PVC
    
# Uso de quotas en Kubernetes

                CAPACIDAD REAL      LO QUE TIENE COMPROMETIDO         USO REAL
                RAM     CPU         RAM     CPU                       RAM     CPU
Maquina 1       16Gbs   8 Cpu               
    POD MariaDB                     8       5                         4 Gbs->6Gbs 
    POD WP                          8       3                         11 Gbs            Me crujo el pod y lo reinicio
Maquina 2       16Gbs   8 Cpu
    
POD MARIADB 
Limit           Hasta donde puedo llegar                16 Gbs  8 cores
Request         Lo que kubernetes debe garantizarme      8 Gbs  5 cores

POD WP
Limit           Hasta donde puedo llegar                16 Gbs  8 cores
Request         Lo que kubernetes debe garantizarme     16 Gbs  3 cores

Por eso, el request y el limit de memoria los solemos configurar iguales.

JVM -xms600m  -xmx600m 


# Openshift no permite usar imagenes de contenedor que ejecuten los procesos con usuario root
Casi todas las imagenes que encontreis en docker_hub usan ell usuario root para ejecuatr los procesos.
Esto se puede desbloquear... pero no está recomendado