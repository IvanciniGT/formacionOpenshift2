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