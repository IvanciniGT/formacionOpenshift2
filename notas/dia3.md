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