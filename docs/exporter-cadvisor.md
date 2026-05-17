# cAdvisor - Javier Torre Núñez

## ¿Qué es y para qué sirve?

cAdvisor es una herramienta creada por Google que permite monitorizar contenedores Docker y recopilar métricas sobre el uso de recursos del sistema.

Expone métricas compatibles con Prometheus, permitiendo supervisar CPU, memoria, red y almacenamiento de los contenedores en tiempo real.

---

# ¿Cómo se instala y configura?

## 1. Ejecutar cAdvisor con Docker

```bash
sudo docker run -d \
  --name=cadvisor \
  --restart=always \
  -p 8080:8080 \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --privileged \
  gcr.io/cadvisor/cadvisor:latest
```

## 2. Verificar que funciona

```bash
sudo docker ps
```

Comprobar desde el navegador:

```text
http://127.0.0.1:8080
```

Verificar métricas:

```bash
curl http://127.0.0.1:8080/metrics
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
  - job_name: "cadvisor"
    static_configs:
      - targets: ["localhost:8080"]
```

Reiniciar Prometheus:

```bash
sudo systemctl restart prometheus
sudo systemctl status prometheus
```

---

# Métricas principales

## container_cpu_usage_seconds_total

**Tipo:** Counter

**Qué mide:**  
Tiempo total de CPU utilizado por un contenedor.

**Por qué es útil:**  
Permite detectar contenedores con consumo elevado de CPU.

---

## container_memory_usage_bytes

**Tipo:** Gauge

**Qué mide:**  
Cantidad de memoria RAM utilizada por un contenedor.

**Por qué es útil:**  
Ayuda a detectar fugas de memoria o consumo excesivo.

---

## container_network_receive_bytes_total

**Tipo:** Counter

**Qué mide:**  
Cantidad total de datos recibidos por red.

**Por qué es útil:**  
Permite monitorizar el tráfico de entrada del contenedor.

---

## container_fs_usage_bytes

**Tipo:** Gauge

**Qué mide:**  
Espacio en disco utilizado por el contenedor.

**Por qué es útil:**  
Ayuda a evitar problemas por falta de almacenamiento.

---

# Ejemplo de alerta

Si el uso de memoria de un contenedor supera el 80% durante más de 5 minutos, se disparará una alerta de "Alto consumo de memoria".

Esto indicaría que el contenedor está utilizando demasiados recursos y podría afectar al rendimiento del servidor o provocar caídas de la aplicación.

---

# Limitaciones

cAdvisor monitoriza recursos de contenedores, pero no analiza directamente el funcionamiento interno de las aplicaciones.

Además, en entornos con muchos contenedores puede generar una gran cantidad de métricas y aumentar el consumo de recursos del sistema.
