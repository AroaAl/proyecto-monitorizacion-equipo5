# MySQL Exporter - Javier Mohino

## ¿Qué es y para qué sirve?

MySQL Exporter es una herramienta que permite recopilar métricas de servidores MySQL y exponerlas en un formato compatible con Prometheus.

Su principal función es monitorizar el estado, rendimiento, conexiones y consultas de bases de datos MySQL en tiempo real.

---

# ¿Cómo se instala y configura?

## 1. Ejecutar MySQL Exporter con Docker

```bash
sudo docker run -d \
  --name=mysql-exporter \
  -p 9104:9104 \
  -e DATA_SOURCE_NAME="usuario:contraseña@(127.0.0.1:3306)/" \
  prom/mysqld-exporter
```

---

## 2. Verificar que funciona

```bash
sudo docker ps
```

Comprobar métricas:

```bash
curl http://127.0.0.1:9104/metrics
```

---

## 3. Configurar Prometheus

Editar el archivo:

```bash
sudo nano /etc/prometheus/prometheus.yml
```

Añadir el job:

```yaml
scrape_configs:
  - job_name: "mysql"
    static_configs:
      - targets: ["localhost:9104"]
```

Reiniciar Prometheus:

```bash
sudo systemctl restart prometheus
sudo systemctl status prometheus
```

---

# Métricas principales

## mysql_global_status_threads_connected

**Tipo:** Gauge

**Qué mide:**  
Número de conexiones activas en MySQL.

**Por qué es útil:**  
Permite detectar exceso de conexiones simultáneas.

---

## mysql_global_status_queries

**Tipo:** Counter

**Qué mide:**  
Cantidad total de consultas ejecutadas.

**Por qué es útil:**  
Ayuda a analizar la carga de trabajo de la base de datos.

---

## mysql_global_status_slow_queries

**Tipo:** Counter

**Qué mide:**  
Número de consultas lentas ejecutadas.

**Por qué es útil:**  
Permite detectar problemas de rendimiento en MySQL.

---

## mysql_global_status_uptime

**Tipo:** Counter

**Qué mide:**  
Tiempo que MySQL lleva funcionando.

**Por qué es útil:**  
Ayuda a detectar reinicios inesperados del servicio.

---

# Ejemplo de alerta

Si el número de consultas lentas supera 10 durante más de 5 minutos, se disparará una alerta de "Bajo rendimiento en MySQL".

Esto indicaría que la base de datos está tardando demasiado en responder y podría afectar al funcionamiento de las aplicaciones conectadas.

---

# Limitaciones

MySQL Exporter monitoriza métricas del servidor MySQL, pero no analiza directamente el contenido de las consultas ni optimiza automáticamente el rendimiento.

Además, en bases de datos muy grandes puede generar una gran cantidad de métricas.
