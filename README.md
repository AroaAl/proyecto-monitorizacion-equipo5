# PROYECTO MONITORIZACIÓN EQUIPO 5

- AROA ALONSO experta en Blackbox Exporter
- MARÍA MARTINEZ experta en Nginx
- JAVIER TORRE experto en cAdvisor
- CRUZ BUSTOS experto en Process Exporter
- GERMAN LIEBY experto en Node Exporter
- JAVIER MOHINO experto en MySQL Exporter
  
# Descripción de la aplicación monitorizada
# Arquitectura
Aplicación Web
│

├── Node Exporter (sistema operativo)

├── Blackbox Exporter (estado de la web)

├── MySQL Exporter (base de datos)

├── cAdvisor (contenedores / recursos)

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
## Node Exporter
Se debe instalar con el comando docker run docker run -p 9113:9113 nginx/nginx-prometheus-exporter:1.5.1 --nginx.scrape-uri=http://<nginx>:8080/stub_status

# Decisiones que hemos tomado

Hemos elegido estos exporters porque cada uno monitoriza una parte distinta del sistema:

Node Exporter → estado del sistema operativo

Blackbox Exporter → disponibilidad de la web

MySQL Exporter → estado de la base de datos

cAdvisor → uso de recursos y contenedores

Process Exporter → procesos activos

Se han utilizado principalmente los puertos por defecto de cada exporter. Se descartaron otros exporters ya que no eran utiles para nuestro caso.
