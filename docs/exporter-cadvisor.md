# cAdvisor

## ¿Qué es y para qué sirve?

Es una herramienta de monitorización creada por Google que permite analizar el uso de recursos y el rendimiento de los contenedores Docker.

Su principal función es monitorizar CPU, memoria, red y disco de los contenedores en tiempo real.

---

# ¿Cómo se instala y configura?

## Ejecutar cAdvisor con Docker

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

## Configuración con Prometheus

Editar el archivo:

```bash
sudo nano /etc/prometheus/prometheus.yml
```

Añadir:

```yaml
# cAdvisor
- job_name: 'cadvisor'
  static_configs:
    - targets:
        - '127.0.0.1:8080'
```

Reiniciar Prometheus:

```bash
sudo systemctl restart prometheus
```

---

# Puerto por defecto

Puerto por defecto:

```text
8080
```

---

# Métricas principales

## container_cpu_usage_seconds_total

**Tipo:** Counter

**Qué mide:**  
Uso total de CPU del contenedor.

**Por qué es útil:**  
Permite detectar contenedores con alto consumo de CPU.

---

## container_memory_usage_bytes

**Tipo:** Gauge

**Qué mide:**  
Memoria RAM utilizada por el contenedor.

**Por qué es útil:**  
Ayuda a detectar consumo excesivo de memoria.

---

## container_network_receive_bytes_total

**Tipo:** Counter

**Qué mide:**  
Cantidad de datos recibidos por red.

**Por qué es útil:**  
Permite monitorizar el tráfico de entrada.

---

## container_fs_usage_bytes

**Tipo:** Gauge

**Qué mide:**  
Espacio en disco utilizado por el contenedor.

**Por qué es útil:**  
Evita problemas por falta de almacenamiento.

---

# Alertas

Una alerta útil sería:

> Si el uso de memoria de un contenedor supera el 80% durante 5 minutos, enviar una alerta de alto consumo de memoria.

---

# Limitaciones

cAdvisor monitoriza recursos de contenedores, pero no analiza directamente el estado interno de las aplicaciones.

Además, en entornos con muchos contenedores puede generar una gran cantidad de métricas.
