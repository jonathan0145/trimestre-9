## Sistemas Operativos (versiones y ediciones)

El sistema está diseñado para funcionar principalmente en entornos basados en:

- **Servidor/Backend:**
	- Windows Server 2016/2019/2022
	- Ubuntu Server 20.04/22.04 LTS
	- Cualquier sistema compatible con Node.js v18+ y Docker

- **Frontend:**
	- Windows 10/11
	- macOS 12+
	- Linux (Ubuntu, Fedora, Debian)
	- Navegadores modernos: Chrome, Firefox, Edge, Safari (últimas versiones)

Se recomienda el uso de Docker para facilitar la compatibilidad y despliegue en diferentes sistemas operativos.

## Aplicaciones existentes (posibles conflictos o integraciones)

- **Integraciones recomendadas:**
	- PostgreSQL (base de datos principal)
	- Redis (cache y sesiones)
	- AWS S3 o servicios compatibles para almacenamiento de archivos
	- Nginx como proxy reverso

- **Posibles conflictos:**
	- Otros servicios que utilicen los mismos puertos (por defecto: 3000 para backend, 8080 para frontend)
	- Instalaciones previas de Node.js en versiones incompatibles (< v18)
	- Firewalls o antivirus que bloqueen WebSocket o puertos necesarios
	- Versiones antiguas de Docker o incompatibilidad con Docker Compose

No se han identificado conflictos graves con aplicaciones de ofimática, navegadores o clientes de correo. Se recomienda revisar las configuraciones de red y puertos antes de la instalación en entornos compartidos.
