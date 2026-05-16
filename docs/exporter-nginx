# Nginx Prometheus Exporter

## ¿Qué es y para qué sirve?

**Nginx Exporter** recoge métricas de un servidor Nginx y las expone en un formato que herramientas como **Prometheus** puedan entender, así las recolecta y las almacena.

---

## ¿Cómo se instala y configura?

### 1. Descargar e instalar el exporter

```bash
wget https://github.com/nginx/nginx-prometheus-exporter/releases/download/v1.5.1/nginx-prometheus-exporter_1.5.1_linux_amd64.tar.gz
tar -xzvf nginx-prometheus-exporter_1.5.1_linux_amd64.tar.gz
sudo mv nginx-prometheus-exporter /usr/local/bin/
sudo chmod +x /usr/local/bin/nginx-prometheus-exporter
nginx-prometheus-exporter --version
```

---

### 2. Habilitar `stub_status` en Nginx

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

#### 2.1 Verificar que responde

```bash
curl http://127.0.0.1:8080/stub_status
```

---

### 3. Crear un usuario dedicado

```bash
sudo useradd --no-create-home --shell /bin/false nginx_exporter
```

---

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

---

### 5. Arrancar y habilitar el servicio

```bash
sudo systemctl daemon-reload
sudo systemctl enable nginx-prometheus-exporter
sudo systemctl start nginx-prometheus-exporter
sudo systemctl status nginx-prometheus-exporter
```

---

### 6. Verificar que expone métricas

```bash
curl http://localhost:9113/metrics | grep nginx_
```

---

### 7. Añadir el job en Prometheus

Editar `/etc/prometheus/prometheus.yml` y añadir:

```yaml
scrape_configs:
  - job_name: "nginx"
    static_configs:
      - targets: ["localhost:9113"]
```

Luego reiniciar Prometheus:

```bash
sudo systemctl restart prometheus
sudo systemctl status prometheus
```
