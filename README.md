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
