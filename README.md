# PROYECTO MONITORIZACIÓN EQUIPO 5

- AROA ALONSO experta en Blackbox Exporter
- MARÍA MARTINEZ experta en Nginx
- JAVIER TORRE experto en cAdvisor
- CRUZ BUSTOS experto en Process Exporter
- GERMAN LIEBY experto en Node Exporter
- JAVIER MOHINO experto en MySQL Exporter
  
# Descripción de la aplicación monitorizada

Aplicación web de gestión de componentes construida sobre Node.js, tiene una base de datos MariaDB y un servidor Apache.
Sus métricas son recogidas automáticamente por Prometheus y visualizadas en dashboards de Grafana.

Endpoints:
    - Comunicación cliente servidor
    - Base de datos
    - Proxy
    - Métricas a desarrollar

Serviría para controlar y administrar el inventario de piezas o componentes de una organización, permitiendo registrar, consultar, actualizar y eliminar componentes del catálogo con toda su información asociada.

# Arquitectura
Aplicación Web
│

├── Node Exporter (sistema operativo)

├── Blackbox Exporter (estado de la web)

├── MySQL Exporter (base de datos)

├── cAdvisor (contenedores / recursos)

├── Nginx (capa web)

└── Process Exporter (procesos)

│

▼
Prometheus (recoge y almacena métricas)

# Instalación exporters
## BlackBox
Descargamos el exporter
```
wget https://github.com/prometheus/blackbox_exporter/releases/latest/download/blackbox_exporter-linux-amd64.tar.gz
```
Descromprimimos

```
tar -xvzf blackbox_exporter-linux-amd64.tar.gz
```
 Ejecutamos el programa

```
cd blackbox_exporter
./blackbox_exporter
```
## cAdvisor
Descargamos el exporter
```
wget https://github.com/google/cadvisor/releases/latest/download/cadvisor-linux-amd64
```

Damos permisos
```
chmod +x cadvisor-linux-amd64

```
Ejecutamos
```
./cadvisor-linux-amd64
```

## Node Exporter
Se debe instalar con el comando docker "docker run ...":
```
docker run -p 9113:9113 nginx/nginx-prometheus-exporter:1.5.1 --nginx.scrape-uri=http://<nginx>:8080/stub_status
```
## Nginx
Descargamos el exporter
```
wget https://github.com/nginxinc/nginx-prometheus-exporter/releases/latest/download/nginx-prometheus-exporter_linux_amd64.tar.gz 
```
Descromprimimos

```
tar -xvzf nginx-prometheus-exporter_linux_amd64.tar.gz
```
 Ejecutamos el programa

```
./nginx-prometheus-exporter
```

## Process
Descargamos el exporter
```
wget https://github.com/ncabatoff/process-exporter/releases/latest/download/process-exporter_linux_amd64.tar.gz
```
Descromprimimos

```
tar -xvzf process-exporter_linux_amd64.tar.gz
```
 Ejecutamos el programa

```
./process-exporter --config.path process-exporter.yml
```

# Decisiones que hemos tomado

Hemos elegido estos exporters porque cada uno monitoriza una parte distinta del sistema:

Node Exporter → estado del sistema operativo

Blackbox Exporter → disponibilidad de la web

MySQL Exporter → estado de la base de datos

cAdvisor → uso de recursos y contenedores

Process Exporter → procesos activos

Nginx Exporter → coneciones y peticiones HTTP

Se han utilizado principalmente los puertos por defecto de cada exporter. Se descartaron otros exporters ya que no eran utiles para nuestro caso.
