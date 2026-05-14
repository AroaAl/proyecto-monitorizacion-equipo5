### ¿Qué es y para qué sirve?
Es una herramienta de monitorización que permite analizar los servidores web, donde su principal función es analizar el estado de la web (levantada o caída) y su rendimiento.

### ¿Cómo se instala y configura?

1. Descargar el binario
Ve al repositorio oficial de GitHub y descarga la versión más reciente. Copia el archivo al directorio /tmp y descomprímelo con tar.
2. Crear el servicio systemd
     ``` bash
     sudo nano /etc/systemd/system/blackbox_exporter.service
     ```
     Editamos del fichero:
```ini
[Unit]
Description=Prometheus Blackbox Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=tu_usuario
Type=simple
ExecStart=/tmp/blackbox_exporter-0.28.0.linux-amd64/blackbox_exporter \
  --config.file=/tmp/blackbox_exporter-0.28.0.linux-amd64/blackbox.yml \
  --web.listen-address=":9115"

[Install]
WantedBy=multi-user.target
```
3. Arrancar el servicio

```bash
sudo systemctl daemon-reload
sudo systemctl start blackbox_exporter
sudo systemctl status blackbox_exporter
```
### Configuración con Prometheus

Edita el archivo de Prometheus:

```bash
sudo nano /etc/prometheus/prometheus.yml
```

Añade lo siguiente:

```yaml
# BlackBox Exporter
- job_name: 'blackbox_prueba'
  metrics_path: /probe
  params:
    module: [http_2xx]
  static_configs:
    - targets:
        - https://www.google.com
        - https://www.ubuntu.com
  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target
    - source_labels: [__param_target]
      target_label: instance
    - target_label: __address__
      replacement: 127.0.0.1:9115
```
Para confirmar la nueva configuración:

```bash
sudo systemctl restart prometheus
```
### Puerto por defecto
Puerto por defecto "9115"

### Métricas principales

### `probe_success`
- **Tipo:** Gauge
- **Qué mide:** Devuelve `1` si la web está activa, `0` si no.
- **Por qué es útil:** Indica si el servicio está disponible desde el exterior.

### `probe_duration_seconds`
- **Tipo:** Gauge
- **Qué mide:** Tiempo total (en segundos) que tardó en completarse la petición.
- **Por qué es útil:** Detecta cuellos de botella; una web que tarda 10 s está prácticamente caída para el usuario.

### `probe_ssl_earliest_cert_expiry`
- **Tipo:** Gauge
- **Qué mide:** Fecha y hora exacta en la que caduca el certificado SSL/TLS.
- **Por qué es útil:** Evita olvidar renovar el certificado, lo que haría que los navegadores bloqueen el acceso marcando la web como "No segura".

### Alertas

Una alerta que tendría sentido basada en una de esas métricas: "Si la métrica de tiempo de respuesta total (**probe_duration_seconds**) supera los 5 segundos de forma continua durante 3 minutos, enviar una alerta de Degradación de Servicio Web."
