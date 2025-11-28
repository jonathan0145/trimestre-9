# Procedimientos de Rollback - Fase 9: Archivos y Almacenamiento

## Información de la Fase

**Nombre de la Fase:** Archivos y Almacenamiento
**Número de Fase:** 9
**Fecha de Inicio:** 17/02/2026
**Fecha de Fin:** 24/02/2026
**Responsable de Rollback:** Ricardo Fernández (DevOps Lead)
**Coordinador de Crisis:** Miguel Rodríguez (CTO)

---

## 🎯 Objetivo del Plan de Rollback

### Propósito Principal
Establecer procedimientos claros, rápidos y confiables para **revertir completamente** la migración del sistema de archivos de AWS S3 al almacenamiento local en caso de problemas críticos que impacten la operación del negocio o la experiencia del usuario.

### Principios de Diseño
- ⚡ **Velocidad:** Rollback completo en <2 horas
- 🔒 **Seguridad:** Sin pérdida de datos durante la reversión  
- 🎯 **Precisión:** Estado exacto pre-migración restaurado
- 👥 **Comunicación:** Updates claros durante todo el proceso

---

## 🚨 Escenarios de Activación de Rollback

### 🔴 Criterios de Rollback Automático

#### **Nivel 1: Fallas Críticas del Sistema**
```yaml
Triggers Inmediatos:
  - AWS S3 availability <95% por >30 minutos
  - CloudFront CDN errors >20% por >15 minutos
  - File access success rate <90% por >10 minutos
  - Application errors relacionados con archivos >50/hora
  - Security breach confirmada en S3 buckets

Response Time: Activación automática en 5 minutos
Decision Maker: Sistema automatizado + confirmación CTO
```

#### **Nivel 2: Problemas de Performance**
```yaml
Triggers de Performance:
  - File download latency >10 segundos promedio por >30 min
  - File upload success rate <80% por >20 min
  - CDN cache miss ratio >50% persistente
  - Database query timeout por file operations >15/hora
  - Mobile app crash rate >10% relacionado con archivos

Response Time: Evaluación en 15 minutos, decisión en 30 min
Decision Maker: Migration Lead + CTO
```

#### **Nivel 3: Impacto de Negocio**
```yaml
Business Impact Triggers:
  - User satisfaction score <6/10 en primeras 4 horas
  - Support tickets relacionados con archivos >50% increase
  - Critical real estate transactions blocked por file issues
  - Agent productivity decrease >30% relacionado con archivos
  - Revenue impact estimado >€10,000/day

Response Time: Evaluación en 1 hora, decisión ejecutiva
Decision Maker: CTO + VP Producto + CEO (si aplica)
```

---

## 📊 Matriz de Decisión de Rollback

### 🎯 Decision Framework

| Severidad | Impacto | Tiempo Resolución Estimado | Acción Recomendada |
|-----------|---------|---------------------------|-------------------|
| 🔴 Crítico | Alto | >4 horas | **ROLLBACK INMEDIATO** |
| 🟡 Alto | Alto | 2-4 horas | **ROLLBACK PREVENTIVO** |
| 🟡 Alto | Medio | 1-2 horas | **Fix Forward** + Monitor |
| 🟢 Medio | Bajo | <1 hora | **Hot Fix** + Continue |

### 🎲 Análisis de Riesgo vs Beneficio

#### **Factores Pro-Rollback:**
- Impacto en usuarios finales (agentes inmobiliarios)
- Pérdida potencial de revenue por transactions bloqueadas
- Damage a reputación de la plataforma
- Costo de soporte incremental durante problemas
- Compliance y legal implications

#### **Factores Anti-Rollback:**
- Effort y tiempo ya invertido en migración
- Potential data inconsistencies durante rollback
- Re-work futuro para nueva migración
- Team morale y confidence impact
- Stakeholder perception y business credibility

---

## 🔄 Procedimientos de Rollback por Componente

### 🗄️ Rollback de Base de Datos (Prioridad 1)

#### **Paso 1: Backup de Estado Actual (5 minutos)**
```sql
-- 1.1 Snapshot rápido del estado actual para forensics
CREATE TABLE properties_files_migration_state AS 
SELECT * FROM properties_files WHERE updated_at >= '2026-02-17 00:00:00';

CREATE TABLE user_files_migration_state AS 
SELECT * FROM user_files WHERE updated_at >= '2026-02-17 00:00:00';

CREATE TABLE contract_files_migration_state AS 
SELECT * FROM contract_files WHERE updated_at >= '2026-02-17 00:00:00';

-- 1.2 Export crítico para análisis posterior
mysqldump inmotech \
    properties_files_migration_state \
    user_files_migration_state \
    contract_files_migration_state \
    > rollback_forensics_$(date +%Y%m%d_%H%M%S).sql
```

#### **Paso 2: Restauración de Paths (10 minutos)**
```sql
-- 2.1 Restore properties_files paths to local storage
UPDATE properties_files 
SET file_path = CASE
    WHEN file_path LIKE 'https://cdn.inmotech.com/%' 
    THEN CONCAT('/var/www/inmotech/uploads/', SUBSTRING(file_path, 31))
    WHEN file_path LIKE 'https://s3.amazonaws.com/inmotech-prod-documents/%'
    THEN CONCAT('/var/www/inmotech/uploads/', SUBSTRING(file_path, 52))
    WHEN file_path LIKE 'https://inmotech-prod-media.s3.amazonaws.com/%'
    THEN CONCAT('/var/www/inmotech/uploads/', SUBSTRING(file_path, 48))
    ELSE file_path
END,
updated_at = NOW()
WHERE file_path LIKE 'https://%' 
  AND updated_at >= '2026-02-17 00:00:00';

-- 2.2 Restore user_files paths
UPDATE user_files
SET file_path = CASE
    WHEN file_path LIKE 'https://cdn.inmotech.com/%'
    THEN CONCAT('/var/www/inmotech/uploads/', SUBSTRING(file_path, 31))
    WHEN file_path LIKE 'https://s3.amazonaws.com/inmotech-prod-documents/%'
    THEN CONCAT('/var/www/inmotech/uploads/', SUBSTRING(file_path, 52))
    ELSE file_path
END,
updated_at = NOW()
WHERE file_path LIKE 'https://%'
  AND updated_at >= '2026-02-17 00:00:00';

-- 2.3 Restore contract_files paths
UPDATE contract_files
SET document_path = CASE
    WHEN document_path LIKE 'https://s3.amazonaws.com/inmotech-prod-documents/%'
    THEN CONCAT('/var/www/inmotech/uploads/', SUBSTRING(document_path, 52))
    ELSE document_path  
END,
updated_at = NOW()
WHERE document_path LIKE 'https://%'
  AND updated_at >= '2026-02-17 00:00:00';
```

#### **Paso 3: Validación de Rollback de DB (5 minutos)**
```sql
-- 3.1 Verificar que no quedan URLs de AWS
SELECT 
    'properties_files' as table_name,
    COUNT(*) as total_records,
    COUNT(CASE WHEN file_path LIKE 'https://%' THEN 1 END) as aws_paths_remaining,
    COUNT(CASE WHEN file_path LIKE '/var/www/%' THEN 1 END) as local_paths_restored
FROM properties_files
WHERE updated_at >= '2026-02-17 00:00:00'
UNION ALL
SELECT 
    'user_files' as table_name,
    COUNT(*) as total_records,
    COUNT(CASE WHEN file_path LIKE 'https://%' THEN 1 END) as aws_paths_remaining,
    COUNT(CASE WHEN file_path LIKE '/var/www/%' THEN 1 END) as local_paths_restored
FROM user_files
WHERE updated_at >= '2026-02-17 00:00:00'
UNION ALL
SELECT 
    'contract_files' as table_name,
    COUNT(*) as total_records,
    COUNT(CASE WHEN document_path LIKE 'https://%' THEN 1 END) as aws_paths_remaining,
    COUNT(CASE WHEN document_path LIKE '/var/www/%' THEN 1 END) as local_paths_restored
FROM contract_files
WHERE updated_at >= '2026-02-17 00:00:00';

-- 3.2 Success criteria: aws_paths_remaining = 0 para todas las tablas
```

---

### 📁 Rollback de Sistema de Archivos (Prioridad 2)

#### **Opción A: Archivos ya en Local (Caso Ideal)**
```bash
#!/bin/bash
# Script: rollback_filesystem_local.sh

echo "🔄 Iniciando rollback de filesystem - Archivos locales disponibles"

# 1. Verificar que archivos locales siguen intactos
UPLOAD_DIR="/var/www/inmotech/uploads"
BACKUP_DIR="/backup/uploads_pre_migration"

if [ -d "$UPLOAD_DIR" ] && [ "$(du -s $UPLOAD_DIR | cut -f1)" -gt 1000000 ]; then
    echo "✅ Archivos locales siguen disponibles"
    ROLLBACK_METHOD="local_available"
else
    echo "⚠️ Archivos locales no disponibles, iniciando download desde S3"
    ROLLBACK_METHOD="download_from_s3"
fi

# 2. Restaurar configuración de servidor web para serving local
echo "🔧 Configurando servidor web para archivos locales..."

# 2.1 Apache configuration
sudo tee /etc/apache2/sites-available/inmotech-files.conf > /dev/null <<EOF
<VirtualHost *:80>
    DocumentRoot /var/www/inmotech/uploads
    Alias /uploads /var/www/inmotech/uploads
    
    <Directory /var/www/inmotech/uploads>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
        
        # Security headers
        Header always set X-Content-Type-Options nosniff
        Header always set X-Frame-Options DENY
    </Directory>
</VirtualHost>
EOF

sudo a2ensite inmotech-files
sudo systemctl reload apache2

# 2.2 Nginx configuration (si aplica)
sudo tee /etc/nginx/sites-available/inmotech-files > /dev/null <<EOF
server {
    listen 80;
    server_name files.inmotech.com;
    root /var/www/inmotech/uploads;
    
    location /uploads/ {
        alias /var/www/inmotech/uploads/;
        expires 7d;
        add_header Cache-Control "public, no-transform";
    }
    
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|pdf)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
EOF

sudo nginx -t && sudo systemctl reload nginx

echo "✅ Servidor web configurado para serving local"
```

#### **Opción B: Download desde S3 (Contingencia)**
```bash
#!/bin/bash
# Script: rollback_download_s3.sh

echo "📥 Iniciando download de archivos desde S3 para rollback..."

# 1. Setup de directorios locales
UPLOAD_DIR="/var/www/inmotech/uploads"
sudo mkdir -p $UPLOAD_DIR/{properties/{photos,videos,documents},agents/{profiles,certifications},contracts/{templates,signed}}

# 2. Download paralelo desde S3
echo "📥 Downloading archivos críticos desde S3..."

# 2.1 Fotos de propiedades (prioridad alta)
aws s3 sync s3://inmotech-prod-media/properties/photos/ \
    $UPLOAD_DIR/properties/photos/ \
    --no-progress \
    --exclude "*.tmp" \
    &

# 2.2 Documentos críticos (paralelo)
aws s3 sync s3://inmotech-prod-documents/properties/documents/ \
    $UPLOAD_DIR/properties/documents/ \
    --no-progress \
    &

aws s3 sync s3://inmotech-prod-documents/contracts/ \
    $UPLOAD_DIR/contracts/ \
    --no-progress \
    &

# 2.3 Videos (baja prioridad, background)
nohup aws s3 sync s3://inmotech-prod-media/properties/videos/ \
    $UPLOAD_DIR/properties/videos/ \
    --no-progress > /var/log/rollback_videos.log 2>&1 &

# 2.4 Esperar a downloads críticos
wait

echo "✅ Download de archivos críticos completado"

# 3. Verificación de integridad
echo "🔍 Verificando integridad de archivos descargados..."
PHOTOS_COUNT=$(find $UPLOAD_DIR/properties/photos -name "*.jpg" -o -name "*.png" | wc -l)
DOCS_COUNT=$(find $UPLOAD_DIR/properties/documents -name "*.*" | wc -l)

echo "📊 Archivos descargados:"
echo "  - Fotos: $PHOTOS_COUNT"  
echo "  - Documentos: $DOCS_COUNT"

# Success criteria: >80% de archivos críticos descargados
if [ $PHOTOS_COUNT -gt 360000 ] && [ $DOCS_COUNT -gt 100000 ]; then
    echo "✅ Rollback download exitoso"
    exit 0
else
    echo "❌ Rollback download falló - archivos insuficientes"
    exit 1
fi
```

---

### 🌐 Rollback de Configuración de Red (Prioridad 3)

#### **DNS Rollback:**
```bash
#!/bin/bash
# Script: rollback_dns.sh

echo "🌐 Iniciando rollback de configuración DNS..."

# 1. Route53 DNS rollback
aws route53 change-resource-record-sets \
    --hosted-zone-id Z123456789ABCDEF \
    --change-batch '{
        "Comment": "Rollback DNS for file storage",
        "Changes": [
            {
                "Action": "UPSERT",
                "ResourceRecordSet": {
                    "Name": "cdn.inmotech.com",
                    "Type": "CNAME", 
                    "TTL": 300,
                    "ResourceRecords": [
                        {"Value": "files.inmotech.com"}
                    ]
                }
            },
            {
                "Action": "DELETE",
                "ResourceRecordSet": {
                    "Name": "cdn.inmotech.com",
                    "Type": "CNAME",
                    "TTL": 300,
                    "ResourceRecords": [
                        {"Value": "d1234567890.cloudfront.net"}
                    ]
                }
            }
        ]
    }'

echo "✅ DNS rollback completado"

# 2. Verificar propagación DNS
echo "🔍 Verificando propagación DNS..."
for i in {1..30}; do
    RESOLVED=$(dig +short cdn.inmotech.com)
    if [[ $RESOLVED == "files.inmotech.com" ]]; then
        echo "✅ DNS propagado correctamente: $RESOLVED"
        break
    else
        echo "⏳ Esperando propagación DNS... ($i/30)"
        sleep 10
    fi
done
```

#### **Load Balancer Configuration:**
```bash
#!/bin/bash
# Script: rollback_load_balancer.sh

echo "⚖️ Configurando load balancer para serving local..."

# 1. HAProxy rollback configuration
sudo tee /etc/haproxy/haproxy.cfg > /dev/null <<EOF
global
    daemon
    
defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

frontend file_frontend
    bind *:80
    default_backend local_file_servers

backend local_file_servers
    balance roundrobin
    server web1 127.0.0.1:8080 check
    server web2 127.0.0.1:8081 check backup
EOF

# 2. Restart HAProxy
sudo systemctl restart haproxy
sudo systemctl enable haproxy

echo "✅ Load balancer configured para serving local"
```

---

### 📱 Rollback de Configuración de Aplicación

#### **Backend Configuration Rollback:**
```bash
#!/bin/bash
# Script: rollback_backend_config.sh

echo "🔧 Iniciando rollback de configuración backend..."

# 1. Environment variables rollback
sudo tee /var/www/inmotech/backend/.env > /dev/null <<EOF
# Database
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=inmotech
DB_USERNAME=inmotech_user
DB_PASSWORD=$DB_PASSWORD

# File Storage (LOCAL)
STORAGE_DRIVER=local
STORAGE_LOCAL_PATH=/var/www/inmotech/uploads
STORAGE_URL_PREFIX=https://files.inmotech.com/uploads

# AWS (disabled)
AWS_ACCESS_KEY_ID=""
AWS_SECRET_ACCESS_KEY=""
AWS_DEFAULT_REGION=""
S3_BUCKET_MEDIA=""
S3_BUCKET_DOCUMENTS=""

# CDN (disabled) 
CDN_URL=""
CDN_ENABLED=false

# Cache settings
CACHE_DRIVER=redis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
EOF

# 2. Update Laravel storage configuration
sudo tee /var/www/inmotech/backend/config/filesystems.php > /dev/null <<'EOF'
<?php

return [
    'default' => env('STORAGE_DRIVER', 'local'),
    
    'disks' => [
        'local' => [
            'driver' => 'local',
            'root' => env('STORAGE_LOCAL_PATH', '/var/www/inmotech/uploads'),
            'url' => env('STORAGE_URL_PREFIX', 'https://files.inmotech.com/uploads'),
            'visibility' => 'public',
        ],
        
        's3' => [
            'driver' => 's3',
            'key' => env('AWS_ACCESS_KEY_ID'),
            'secret' => env('AWS_SECRET_ACCESS_KEY'),
            'region' => env('AWS_DEFAULT_REGION'),
            'bucket' => env('S3_BUCKET_MEDIA'),
        ],
    ],
];
EOF

# 3. Clear Laravel caches
cd /var/www/inmotech/backend
sudo -u www-data php artisan config:clear
sudo -u www-data php artisan cache:clear
sudo -u www-data php artisan route:clear

# 4. Restart application services
sudo systemctl restart apache2
sudo systemctl restart nginx
sudo systemctl restart php8.1-fpm

echo "✅ Backend configuration rollback completado"
```

#### **Frontend Configuration Rollback:**
```javascript
// File: rollback_frontend_config.js
// Script para revertir configuración de frontend

console.log('🔧 Iniciando rollback de configuración frontend...');

// 1. Update API endpoints configuration
const rollbackConfig = {
    API_BASE_URL: 'https://api.inmotech.com',
    FILE_UPLOAD_URL: 'https://api.inmotech.com/files/upload',
    FILE_DOWNLOAD_BASE: 'https://files.inmotech.com/uploads',
    CDN_ENABLED: false,
    CDN_BASE_URL: '', // Disabled
    
    // File handling settings
    MAX_FILE_SIZE: 50 * 1024 * 1024, // 50MB
    ALLOWED_EXTENSIONS: ['jpg', 'jpeg', 'png', 'gif', 'pdf', 'doc', 'docx'],
    UPLOAD_CHUNK_SIZE: 1024 * 1024, // 1MB chunks
    
    // Local storage settings
    ENABLE_LOCAL_CACHE: true,
    CACHE_DURATION: 24 * 60 * 60 * 1000, // 24 hours
};

// 2. Write configuration file
const fs = require('fs');
const configPath = '/var/www/inmotech/frontend/src/config/environment.js';

const configContent = `
// Configuration rollback - Local file storage
export const environment = {
    production: true,
    apiUrl: '${rollbackConfig.API_BASE_URL}',
    fileConfig: {
        uploadUrl: '${rollbackConfig.FILE_UPLOAD_URL}',
        downloadBase: '${rollbackConfig.FILE_DOWNLOAD_BASE}',
        cdnEnabled: ${rollbackConfig.CDN_ENABLED},
        cdnBaseUrl: '${rollbackConfig.CDN_BASE_URL}',
        maxFileSize: ${rollbackConfig.MAX_FILE_SIZE},
        allowedExtensions: ${JSON.stringify(rollbackConfig.ALLOWED_EXTENSIONS)},
        uploadChunkSize: ${rollbackConfig.UPLOAD_CHUNK_SIZE}
    }
};
`;

fs.writeFileSync(configPath, configContent);
console.log('✅ Frontend configuration rollback completado');

// 3. Rebuild frontend assets
const { execSync } = require('child_process');
try {
    execSync('cd /var/www/inmotech/frontend && npm run build:production', 
             { stdio: 'inherit' });
    console.log('✅ Frontend rebuild completado');
} catch (error) {
    console.error('❌ Error en frontend rebuild:', error.message);
    process.exit(1);
}
```

---

## ⚡ Script Maestro de Rollback

### 🚀 Master Rollback Script

```bash
#!/bin/bash
# File: master_rollback.sh
# Rollback completo automatizado para Fase 9

set -e  # Exit on any error

# Configuration
SCRIPT_DIR="/var/scripts/rollback"
LOG_FILE="/var/log/rollback_$(date +%Y%m%d_%H%M%S).log"
NOTIFICATION_WEBHOOK="https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK"

# Logging function
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a $LOG_FILE
}

# Notification function
notify() {
    local message="$1"
    local level="${2:-INFO}"
    
    log "$level: $message"
    
    # Slack notification
    curl -X POST -H 'Content-type: application/json' \
        --data "{\"text\":\"🚨 ROLLBACK $level: $message\"}" \
        $NOTIFICATION_WEBHOOK >/dev/null 2>&1 || true
}

# Error handler
handle_error() {
    local exit_code=$?
    local line_number=$1
    notify "ERROR en línea $line_number. Exit code: $exit_code" "ERROR"
    exit $exit_code
}

trap 'handle_error $LINENO' ERR

# Main rollback execution
main() {
    log "🚨 INICIANDO ROLLBACK COMPLETO - FASE 9"
    notify "Iniciando rollback completo del sistema de archivos" "CRITICAL"
    
    # Step 1: Enable maintenance mode (1 min)
    log "🛑 Activando modo mantenimiento..."
    echo "<h1>Sistema en Mantenimiento</h1><p>Estaremos de vuelta en 2 horas.</p>" \
        > /var/www/html/maintenance.html
    
    # Step 2: Database rollback (20 min)
    log "🗄️ Iniciando rollback de base de datos..."
    mysql -u root -p$DB_ROOT_PASSWORD < $SCRIPT_DIR/rollback_database.sql
    
    if [ $? -eq 0 ]; then
        log "✅ Rollback de base de datos completado"
        notify "Database rollback completado exitosamente" "SUCCESS"
    else
        log "❌ Error en rollback de base de datos"
        notify "FALLO EN DATABASE ROLLBACK" "ERROR"
        exit 1
    fi
    
    # Step 3: Filesystem rollback (60 min)
    log "📁 Iniciando rollback de filesystem..."
    
    # Check if local files available
    if [ -d "/var/www/inmotech/uploads" ] && [ "$(du -s /var/www/inmotech/uploads | cut -f1)" -gt 1000000 ]; then
        log "✅ Archivos locales disponibles, usando método rápido"
        bash $SCRIPT_DIR/rollback_filesystem_local.sh
    else
        log "⚠️ Archivos locales no disponibles, descargando desde S3"
        bash $SCRIPT_DIR/rollback_download_s3.sh
        
        if [ $? -ne 0 ]; then
            notify "FALLO CRÍTICO: No se pudieron recuperar archivos" "CRITICAL" 
            exit 1
        fi
    fi
    
    # Step 4: Network configuration rollback (10 min)
    log "🌐 Iniciando rollback de configuración de red..."
    bash $SCRIPT_DIR/rollback_dns.sh
    bash $SCRIPT_DIR/rollback_load_balancer.sh
    
    # Step 5: Application configuration rollback (15 min)
    log "🔧 Iniciando rollback de configuración de aplicaciones..."
    bash $SCRIPT_DIR/rollback_backend_config.sh
    node $SCRIPT_DIR/rollback_frontend_config.js
    
    # Step 6: Service restart y validation (10 min)
    log "🔄 Reiniciando servicios..."
    sudo systemctl restart mysql
    sudo systemctl restart apache2
    sudo systemctl restart nginx
    sudo systemctl restart redis-server
    
    # Step 7: Health checks
    log "🏥 Ejecutando health checks..."
    
    # Database connectivity
    if mysql -u inmotech_user -p$DB_PASSWORD -e "SELECT 1" inmotech >/dev/null 2>&1; then
        log "✅ Database connectivity OK"
    else
        log "❌ Database connectivity FAILED"
        notify "Database health check falló post-rollback" "ERROR"
        exit 1
    fi
    
    # File access test
    if curl -s "https://files.inmotech.com/uploads/test.jpg" >/dev/null; then
        log "✅ File access OK"
    else
        log "❌ File access FAILED"  
        notify "File access health check falló post-rollback" "ERROR"
        exit 1
    fi
    
    # Application health
    if curl -s "https://api.inmotech.com/health" | grep -q "OK"; then
        log "✅ Application health OK"
    else
        log "❌ Application health FAILED"
        notify "Application health check falló post-rollback" "ERROR"
        exit 1
    fi
    
    # Step 8: Disable maintenance mode
    log "✅ Desactivando modo mantenimiento..."
    rm -f /var/www/html/maintenance.html
    
    # Step 9: Final validation y notification
    ROLLBACK_DURATION=$(($(date +%s) - START_TIME))
    
    log "🎉 ROLLBACK COMPLETADO EXITOSAMENTE"
    log "⏱️ Duración total: $((ROLLBACK_DURATION / 60)) minutos"
    
    notify "🎉 ROLLBACK COMPLETADO EXITOSAMENTE en $((ROLLBACK_DURATION / 60)) minutos" "SUCCESS"
    
    # Post-rollback report
    generate_rollback_report
}

# Generate comprehensive rollback report
generate_rollback_report() {
    local report_file="/var/reports/rollback_report_$(date +%Y%m%d_%H%M%S).txt"
    
    cat > $report_file <<EOF
📊 ROLLBACK REPORT - FASE 9 ARCHIVOS Y ALMACENAMIENTO
================================================================
Fecha: $(date)
Duración: $((ROLLBACK_DURATION / 60)) minutos

🔍 VALIDACIONES REALIZADAS:
$(mysql -u inmotech_user -p$DB_PASSWORD -e "
    SELECT 
        'Database paths' as component,
        COUNT(*) as total_records,
        COUNT(CASE WHEN file_path LIKE '/var/www/%' THEN 1 END) as local_paths,
        COUNT(CASE WHEN file_path LIKE 'https://%' THEN 1 END) as aws_paths_remaining
    FROM properties_files" inmotech)

📁 ARCHIVOS VERIFICADOS:
$(find /var/www/inmotech/uploads -type f | wc -l) archivos totales
$(du -sh /var/www/inmotech/uploads | cut -f1) espacio utilizado

🌐 SERVICIOS ESTADO:
$(systemctl is-active mysql) - Database
$(systemctl is-active apache2) - Web Server  
$(systemctl is-active nginx) - Reverse Proxy
$(systemctl is-active redis-server) - Cache

✅ ROLLBACK EXITOSO - Sistema restaurado a estado pre-migración
================================================================
EOF

    log "📊 Reporte de rollback generado: $report_file"
}

# Initialize
START_TIME=$(date +%s)
main "$@"
```

---

## 📞 Plan de Comunicación Durante Rollback

### 🚨 Comunicación de Crisis

#### **Inmediata (0-15 minutos):**
```yaml
Audiencia Primaria:
  - CTO + VP Producto (llamada inmediata)
  - Equipo técnico (Slack #crisis-response)
  - Equipo de soporte (preparación para incremento de tickets)

Mensaje Template:
  "🚨 CRISIS: Iniciando rollback de migración Fase 9
   Motivo: [razón específica]
   ETA resolución: 2 horas
   Impacto: [descripción del impacto]
   Próximo update: 30 minutos"
```

#### **Updates Regulares (cada 30 minutos):**
```yaml
Stakeholders:
  - Leadership team
  - Customer support
  - Key agents (top 20)

Status Template:
  "📊 ROLLBACK UPDATE #X
   Estado: [% completado]
   Próximos pasos: [descripción]
   ETA: [tiempo restante]
   Issues: [problemas si los hay]"
```

#### **Comunicación a Usuarios (1 hora post-inicio):**
```
Subject: 🔧 Mantenimiento en Progreso - Sistema de Archivos

Estimados usuarios,

Estamos realizando mantenimiento en nuestro sistema de archivos para resolver algunos issues técnicos. 

⏰ Tiempo estimado: 2 horas
🎯 Servicios afectados: Subida/descarga de archivos
✅ Servicios disponibles: Todo lo demás funciona normal

Les notificaremos cuando esté completamente resuelto.

Gracias por su paciencia.
```

---

## 📊 Post-Rollback Analysis

### 🔍 Root Cause Analysis Template

#### **Immediate Assessment (Primera hora post-rollback):**
```yaml
1. Timeline Reconstruction:
   - ¿Cuándo comenzaron los problemas?
   - ¿Qué eventos precedieron la decisión de rollback?
   - ¿Hubo warning signs que se perdieron?

2. Technical Analysis:
   - ¿Qué componente falló específicamente?
   - ¿Los logs muestran errores correlacionados?
   - ¿Hubo problemas de configuración vs. de infraestructura?

3. Impact Assessment:
   - ¿Cuántos usuarios fueron afectados?
   - ¿Qué transacciones se perdieron o demoraron?
   - ¿Cuál fue el impacto financiero estimado?

4. Response Evaluation:
   - ¿El rollback se ejecutó según timeline esperado?
   - ¿La comunicación fue efectiva?
   - ¿Qué mejoras se pueden hacer al proceso?
```

#### **Comprehensive Analysis (Primera semana post-rollback):**
```yaml
Learning Outcomes:
  - Identificar gaps en testing pre-migración
  - Evaluar effectiveness de monitoring y alerting
  - Review de decision criteria para rollback
  - Assessment de team preparedness y training

Process Improvements:
  - Updates necesarios a migration playbook
  - Mejoras a rollback procedures
  - Enhanced monitoring y alerting setup
  - Team training y knowledge gaps

Business Impact:
  - Customer satisfaction impact assessment
  - Revenue impact analysis
  - Competitive positioning implications
  - Stakeholder confidence restoration plan
```

### 📈 Métricas de Éxito Post-Rollback

#### **Technical Recovery Metrics:**
```yaml
System Performance:
  Target: Sistema operando al 100% baseline dentro de 4 horas
  Measure: Response times, error rates, availability

Data Integrity:
  Target: 100% de archivos accesibles post-rollback
  Measure: File access success rate, database consistency

User Experience:
  Target: <5% incremento en support tickets
  Measure: Ticket volume, user complaints, feature utilization
```

#### **Business Recovery Metrics:**
```yaml
User Confidence:
  Target: User satisfaction >8/10 dentro de 1 semana
  Measure: User surveys, usage patterns, retention rates

Operational Excellence:
  Target: Lessons learned implementadas dentro de 2 semanas
  Measure: Process improvements, team training completed

Strategic Positioning:
  Target: Migration plan revisado y aprobado dentro de 1 mes
  Measure: Stakeholder confidence, future migration timeline
```

---

## 📋 Checklist Final de Rollback

### ✅ Pre-Rollback Verification

```yaml
Decision Validation:
□ Rollback decision aprobada por CTO + VP Producto
□ Business impact assessment completado
□ Technical assessment de feasibility realizado  
□ Communication plan activado
□ All stakeholders notificados

Technical Readiness:
□ Database backup pre-rollback completado
□ Rollback scripts tested y validados
□ Local file storage capacity verificada
□ Network configuration changes preparadas
□ Monitoring dashboards configurados para rollback

Team Preparation:
□ All team members on standby
□ Roles y responsibilities clarificados
□ Escalation procedures activados
□ External support vendors notificados si aplica
□ Post-rollback analysis plan establecido
```

### ✅ Durante Rollback

```yaml
Execution Monitoring:
□ Progress tracking cada 15 minutos
□ Error logs monitoring activo
□ Performance metrics being tracked
□ Stakeholder updates cada 30 minutos
□ Health checks ejecutándose automáticamente

Risk Mitigation:
□ Backup plans para componentes críticos activados
□ Alternative approaches ready si script principal falla
□ Emergency contacts disponibles
□ External vendor support on standby
□ Business continuity measures en place
```

### ✅ Post-Rollback Validation

```yaml
Technical Verification:
□ Database integrity verified (100% paths rolled back)
□ File accessibility confirmed (sample testing)
□ Application functionality validated
□ Performance baselines restored
□ Security configurations verified

Business Verification:
□ Critical user workflows tested
□ Support ticket volume monitored
□ User communication sent
□ Executive stakeholders updated
□ Financial impact assessed

Recovery Planning:
□ Root cause analysis initiated  
□ Lessons learned session scheduled
□ Process improvements identified
□ Future migration plan revision started
□ Team confidence restoration plan activated
```

---

**Fecha de Creación:** 21/11/2025
**Última Actualización:** 21/11/2025
**Versión:** 1.0