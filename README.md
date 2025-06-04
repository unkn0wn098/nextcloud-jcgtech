# Plataforma de Comunicación y Almacenamiento Empresarial con Docker

Este proyecto proporciona una solución económica, de código abierto y autogestionada para cubrir las necesidades de comunicación, almacenamiento y monitorización de una empresa. Está basado en contenedores Docker y orquestado con Docker Compose.

## 🧹 Servicios Desplegados

- **Nextcloud**: Almacenamiento en la nube y plataforma de colaboración.
- **MariaDB**: Base de datos para Nextcloud.
- **Collabora Online**: Edición colaborativa de documentos desde Nextcloud.
- **Grafana**: Panel de monitorización del entorno.
- **Prometheus**: Recolección de métricas.
- **Nginx Proxy Manager**: Gestión de certificados SSL y proxies reversos.

## 💾 Estructura del Proyecto

```plaintext
Cliente ──> Nginx Proxy Manager ──> [Nextcloud | Collabora | Grafana]
                                 └─> Red interna Docker (proxy_net)
Grafana <── Prometheus <── Exporters (Node, cAdvisor, etc.)
```

Cada servicio corre en su contenedor, conectado a la red interna `proxy_net`, gestionada por Docker.

## ⚙️ Requisitos Previos

- Docker
- Docker Compose
- Un dominio configurado (por ejemplo, `dominio.es`)
- Opcional pero recomendado: gestión DNS con Cloudflare

## 🔐 Variables de Entorno

Las credenciales y configuraciones sensibles están centralizadas en el archivo `.env`:

```dotenv
# Base de datos
MYSQL_ROOT_PASSWORD=change-me
MYSQL_PASSWORD=change-me
MYSQL_DATABASE=change-me
MYSQL_USER=change-me

# Nextcloud
NEXTCLOUD_ADMIN_USER=change-me
NEXTCLOUD_ADMIN_PASSWORD=change-me

# Grafana
GF_SECURITY_ADMIN_USER=change-me
GF_SECURITY_ADMIN_PASSWORD=change-me
```

> ⚠️ **IMPORTANTE:** No subir este archivo a repositorios públicos.

## 🚀 Despliegue Paso a Paso

1. Clona el repositorio:

   ```bash
   git clone https://github.com/tuusuario/tu-repositorio.git
   cd tu-repositorio
   ```

2. Copia y edita el archivo `.env`:

   ```bash
   cp .env.example .env
   nano .env
   ```

3. Levanta los servicios:

   ```bash
   docker-compose up -d
   ```

4. Accede a los servicios:

| Servicio    | URL                                                      |
|-------------|----------------------------------------------------------|
| Nextcloud   | [https://tudominio.com](https://tudominio.com)           |
| Collabora   | Integrado en Nextcloud                                   |
| Grafana     | [https://tudominio.com:3000](https://tudominio.com:3000) |
| Prometheus  | [http://localhost:9090](http://localhost:9090) (local)   |

## 🔧 Configuración de Prometheus

El archivo `prometheus.yml` define los targets y jobs para recoger métricas:

```yaml
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']

  - job_name: 'node'
    static_configs:
      - targets: ['nodeexporter:9100']
```

Asegúrate de que los contenedores `cadvisor` y `nodeexporter` están corriendo en la red `proxy_net`.

## 📊 Monitorización con Grafana

Grafana permite importar dashboards como:

- **Node Exporter Full**
- **Docker Monitoring (cAdvisor)**
- **Prometheus Stats**

Puedes crear alertas sobre uso de CPU, memoria, disco, etc.

## 🔧 Configuración de Collabora

Collabora permite edición online integrada con Nextcloud:

```yaml
- domain=dominio\.es
- extra_params=--o:ssl.enable=false --o:ssl.termination=true ...
```

Debe coincidir el dominio configurado en Nginx Proxy Manager.

## 🔒 Seguridad

- Certificados SSL automatizados con Nginx Proxy Manager
- Gestión de contraseñas mediante `.env`
- Aislamiento de servicios en red privada Docker
- Soporte para 2FA en Nextcloud

## 🧠 Conclusión

Este sistema sustituye plataformas privativas con una alternativa modular, desplegable en cloud o on-premise. Ofrece control total, reducción de costes y flexibilidad.

La plataforma es:

- **Escalable**
- **Modular**
- **Segura**
- **De código abierto**

---

> 📁 Proyecto desarrollado como parte del ciclo de **Administración de Sistemas Informáticos en Red (ASIR)** en el IES Camp de Morvedre.
