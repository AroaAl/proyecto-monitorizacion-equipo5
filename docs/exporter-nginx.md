# María Martínez
# NGINX Prometheus Exporter

## ¿Qué es y para qué sirve?

Nginx Exporter recoge métricas de un servidor Nginx y las expone en un formato que herramientas como por ejemplo Prometheus puedan entender, así las recolecta y las almacena.

---

## ¿Cómo se instala y configura?

### 1. Descargar la versión más reciente del exporter e instalarlo

```bash
wget https://github.com/nginx/nginx-prometheus-exporter/releases/download/v1.5.1/nginx-prometheus-exporter_1.5.1_linux_amd64.tar.gz
tar -xzvf nginx-prometheus-exporter_1.5.1_linux_amd64.tar.gz
sudo mv nginx-prometheus-exporter /usr/local/bin/
sudo chmod +x /usr/local/bin/nginx-prometheus-exporter
nginx-prometheus-exporter --version
```

### 2. Habilitar stub_status en Nginx

```bash
sudo tee /etc/nginx/conf.d/stub_status.conf > /dev/null <<EOF
server {
    listen 8080;
    server_name localhost;

    location /stub_status {
        stub_status on;
        allow 127.0.0.1;
        deny all;
    }
}
EOF

sudo nginx -t && sudo systemctl reload nginx
```

#### 2.1. Verificar que responde

```bash
curl http://127.0.0.1:8080/stub_status
```

### 3. Crear un usuario

```bash
sudo useradd --no-create-home --shell /bin/false nginx_exporter
```

### 4. Crear el servicio systemd

```bash
sudo tee /etc/systemd/system/nginx-prometheus-exporter.service > /dev/null <<EOF
[Unit]
Description=NGINX Prometheus Exporter
After=network.target nginx.service
Requires=nginx.service

[Service]
User=nginx_exporter
Group=nginx_exporter
ExecStart=/usr/local/bin/nginx-prometheus-exporter \
    --nginx.scrape-uri=http://127.0.0.1:8080/stub_status \
    --web.listen-address=:9113
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

### 5. Arrancar y habilitar el servicio

```bash
sudo systemctl daemon-reload
sudo systemctl enable nginx-prometheus-exporter
sudo systemctl start nginx-prometheus-exporter
sudo systemctl status nginx-prometheus-exporter
```

### 6. Verificar que expone métricas

```bash
curl http://localhost:9113/metrics | grep nginx_
```

### 7. Editar `/etc/prometheus/prometheus.yml` y añadir el job

```yaml
scrape_configs:
  - job_name: "nginx"
    static_configs:
      - targets: ["localhost:9113"]
```

```bash
sudo systemctl restart prometheus
sudo systemctl status prometheus
```

---

## Métricas principales

### nginx_http_requests_total

- **Tipo:** Counter
- **Qué mide:** Total acumulado de peticiones HTTP recibidas.
- **Por qué es útil:** Es el pulso del servidor. Obtienes las RPS en tiempo real, base de cualquier alerta de tráfico.

### nginx_connections_active

- **Tipo:** Gauge
- **Qué mide:** Conexiones abiertas en este instante (lectura + escritura + espera).
- **Por qué es útil:** Indica la carga viva del servidor.

### nginx_ingress_controller_request_duration_seconds

- **Tipo:** Histogram
- **Qué mide:** Distribución de la latencia de las peticiones HTTP.
- **Por qué es útil:** Es la métrica de calidad de servicio.

---

## Ejemplo de alerta

Si la tasa de peticiones HTTP procesadas por nginx cae por debajo de un límite mínimo esperado de forma continua durante 3 minutos, se disparará una alerta de "Caída del servicio web".

Esto indicaría que nginx ha dejado de recibir o procesar peticiones con normalidad, lo que puede ser síntoma de una caída del servicio, un problema de red, un reinicio inesperado del proceso o un fallo en el balanceador de carga que está dejando de enrutar tráfico hacia el servidor.

---

## Limitaciones

 No soporta configuración dinámica sin recarga, por lo que si modificas la configuración, necesitas recargar o reiniciar el proceso para aplicar los cambios