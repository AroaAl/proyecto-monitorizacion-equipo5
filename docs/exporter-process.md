# Process Exporter

## 1. ¿Qué es y para qué sirve?

Es un agente que mira proceso a proceso y le comunica a Prometheus cuántos recursos está consumiendo cada uno. Ayuda a detectar cuándo un proceso está consumiendo demasiados recursos.

---

## 2. ¿Cómo se instala y configura?

### 1. Descargar archivo

```bash
wget https://github.com/ncabatoff/process-exporter/releases/download/v0.8.7/process-exporter-0.8.7.linux-amd64.tar.gz
```

### 2. Descomprimir, ver contenido y situarse en el directorio correcto

```bash
tar -xvf process-exporter-*.linux-amd64.tar.gz
ls -d process-exporter-*
cd process-exporter-0.8.7.linux-amd64
```

### 3. Crear la carpeta y el archivo de configuración

```bash
sudo mkdir -p /etc/process-exporter
sudo nano /etc/process-exporter/config.yml
```

```yaml
process_names:
  - name: "nginx"
    cmdline:
      - nginx

  - name: "python-app"
    cmdline:
      - python
```

### 4. Ejecutar para comprobar que funciona

```bash
./process-exporter --config.path /etc/process-exporter/config.yml
```

### 5. Entrar en el archivo de Prometheus.yml

Al añadir este job, estamos indicando que queremos que estas métricas aparezcan en Prometheus.

```yaml
process_names:
  - name: "nginx"
    cmdline:
      - nginx

  - name: "python-app"
    cmdline:
      - python

scrape_configs:
  - job_name: 'process-exporter'
    static_configs:
      - targets: ['localhost:9256']
```

### 6. Reiniciar y verificar

```bash
sudo systemctl restart prometheus
sudo systemctl status prometheus
```

### 7. Editar el archivo yml de Prometheus para añadir el job de Process-Exporter

```yaml
- job_name: "process-exporter"
  static_configs:
    - targets:
        - "localhost:9256"
      labels:
        entorno: "laboratorio"
        equipo: "asir"
```

---

## 3. Métricas

### Uso de CPU por proceso (`namedprocess_namegroup_cpu_seconds_total`)

- **Tipo:** Counter
- **Qué mide:** Total de segundos de CPU consumidos por un grupo de procesos.
- **Por qué es útil:** Permite ver qué proceso exacto se está "comiendo" el servidor.

### Memoria por proceso (`namedprocess_namegroup_memory_bytes`)

- **Tipo:** Gauge (valor instantáneo)
- **Qué mide:** La memoria RAM actual en bytes.
- **Por qué es útil:** Permite monitorizar el consumo de memoria en tiempo real. Detecta si el programa tiene una "fuga de memoria" (va gastando más y más con el tiempo).

### Número de instancias (`namedprocess_namegroup_num_procs`)

- **Tipo:** Gauge
- **Qué mide:** El número de procesos activos.
- **Por qué es útil:** Verifica si el proceso se ha cerrado o si hay demasiadas copias abiertas. Ayuda a ver la concurrencia o escalado del servicio.

---

## 5. Alerta ejemplo

Una alerta que te avise cuando se queda sin memoria RAM.

---

## 6. Una limitación

No indica si hay picos de conexiones fallidas.