``` Germán Lieby Mazzitelli ```

# NODE EXPORTER

## ¿Qué es y para qué sirve?

Este exporter pertenece al ecosistema de Prometheus y sirve para obtener métricas del hardware y del sistema operativo del equipo donde está instalado. Permite monitorizar datos como CPU, memoria, disco o red para conocer el estado y rendimiento del sistema. Además, es un exporter oficial de Prometheus y facilita que esas métricas puedan visualizarse y supervisarse fácilmente.

Paso 1: Creación del usuario de sistema
```
sudo useradd --no-create-home --shell /bin/false node_exporter
```

Paso 2: Descarga e instalación del binario
```
cd /tmp wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
tar xvf node_exporter-1.8.2.linux-amd64.tar.gz 
sudo cp node_exporter-1.8.2.linux-amd64/node_exporter /usr/local/bin/ 
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

Paso 3: Configuración del servicio (Systemd)
```
sudo nano /etc/systemd/system/node_exporter.service
```
Contenido del archivo:
[Unit] 
Description=Node Exporter
After=network.target 
[Service] User=node_exporter 
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter 
[Install] 
WantedBy=multi-user.target

Paso 4: Activación del servicio
```
sudo systemctl daemon-reload 
sudo systemctl enable --now node_exporter
```

Paso 5:  Configuración de Prometheus 

Edición del fichero prometheus.yml
```
sudo nano /etc/prometheus/prometheus.yml
```
Editar el fichero

- job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
        labels:
          entorno: 'laboratorio'
          equipo: 'asir'

Paso 6 : Reinicio del servidor
```
sudo systemctl restart prometheus
```
## Verificación de la Infraestructura

Estado del servicio: Ejecutar sudo systemctl status node_exporter. El estado debe ser activo (running).

Exposición de métricas: Acceder a http://localhost:9100/metrics. Se debe visualizar un listado de métricas en formato de texto plano.

Panel de Prometheus: En la interfaz web (http://localhost:9090), ir a Status -> Targets. El nodo debe aparecer con el estado UP.


## Resolución de Problemas (Troubleshooting).

Error 217 (USER): El servicio falla si el usuario definido en el archivo .service no existe en el sistema. Se soluciona con useradd.
Error 203 (EXEC): El servicio no encuentra el archivo ejecutable. Se soluciona verificando que el binario esté en /usr/local/bin/ y tenga permisos de ejecución (chmod +x).
El puerto al que expone métricas es el por defecto: 9100 y es configurable.

Las tres métricas relevantes que ofrece:
``` node_cpu_seconds_total ```
``` node_memory_MemAvailable_bytes ```
``` node_memory_MemTotal_bytes ```
## Tipo (counter / gauge / histogram).
1. node_cpu_seconds_total
``` Tipo: Counter ```
``` Representa el tiempo total que la CPU ha pasado en un modo específico desde que se inició el sistema. 
Como es un contador que siempre aumenta, casi nunca se usa solo. Se utiliza con la función rate() para calcular el porcentaje de uso de CPU. ```

Fórmula común: 

1 - avg(rate(node_cpu_seconds_total{mode="idle"}[5m]))

2. node_memory_MemTotal_bytes

``` Tipo: Gauge ```

``` Es un valor absoluto que representa la cantidad total de memoria física en el nodo. Aunque técnicamente es un valor que no suele cambiar (a menos que añadas RAM física o cambies la configuración de una VM), se clasifica como Gauge porque representa un "estado" o una medición instantánea, no una acumulación de eventos.
Sirve como base para calcular porcentajes de uso de memoria. ```

3. node_memory_MemAvailable_bytes

Tipo: Gauge.

``` El valor fluctúa constantemente dependiendo de la carga de trabajo del sistema. Sube cuando se liberan procesos y baja cuando el sistema consume más memoria.
A diferencia de node_memory_MemFree_bytes (que solo mide la memoria que no tiene absolutamente nada), MemAvailable es más precisa para alertas, ya que estima cuánta memoria hay realmente disponible para nuevos procesos, incluyendo la memoria que se puede liberar de cachés o buffers.
Mide el rendimiento del sistema operativo y hardware.
Es útil monitorizarla para evitar sobrecargas o puntos muertos. ```

## Alerta:

Una alerta que tendría sentido basada en una de esas métricas (en lenguaje natural, no PromQL aún): "si X supera Y durante Z minutos, avisar".

X (Métrica): La cantidad de memoria disponible (node_memory_MemAvailable_bytes).

Y (Umbral): Menos del 10% del total de la memoria (node_memory_MemTotal_bytes).

Z (Tiempo): Durante 5 minutos.

## Una limitación o cosa que NO mide este exporter.
No mide nada a nivel de aplicación y ni de contenedor solo del sistema 

