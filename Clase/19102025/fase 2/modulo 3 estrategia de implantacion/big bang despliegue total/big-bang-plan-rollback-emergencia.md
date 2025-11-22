# Plan de Rollback de Emergencia - Big Bang InmoTech

## 🚨 Información Crítica del Plan

**Tipo de Despliegue:** Big Bang - Sistema Completo  
**Sistema:** InmoTech - Plataforma Inmobiliaria Integral  
**Fecha:** Noviembre 2025  
**Responsable de Rollback:** Equipo de Emergencia Técnica  
**Tiempo Objetivo de Rollback:** < 30 minutos  
**Versión:** 1.0 - CRÍTICO  

---

## 🎯 Resumen Ejecutivo de Rollback

### 🚨 Objetivo del Plan
**Restaurar completamente el sistema InmoTech al estado funcional anterior al despliegue Big Bang en menos de 30 minutos**, garantizando la continuidad del negocio y la integridad de datos ante cualquier falla crítica durante la implementación.

### 📊 Escenarios de Activación
Este plan se ejecuta automática o manualmente cuando se detecta:
- **Falla crítica del sistema** (>50% de funcionalidad afectada)
- **Corrupción de datos** detectada
- **Performance degradada** (>300% tiempo de respuesta normal)
- **Vulnerabilidades de seguridad** críticas
- **Decisión ejecutiva** ante impacto comercial inaceptable

---

## ⚡ Scripts Maestros de Rollback

### 🔴 Script 1: Rollback Automático de Emergencia (Crítico)

```bash
#!/bin/bash
# emergency_rollback_inmotech.sh
# USAGE: ./emergency_rollback_inmotech.sh "reason for rollback"
# CONTACT: DevOps Team +34 XXX XXX XXX

set -euo pipefail

# Configuration
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
LOG_FILE="/var/log/inmotech/emergency_rollback_${TIMESTAMP}.log"
BACKUP_BASE="/backup/inmotech/pre_bigbang"
ROLLBACK_TIMEOUT=1800  # 30 minutes

# Logging function
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a "$LOG_FILE"
}

# Error handling
error_exit() {
    log "🚨 CRITICAL ERROR: $1"
    log "🚨 MANUAL INTERVENTION REQUIRED"
    
    # Send emergency notification
    curl -X POST "https://hooks.slack.com/services/YOUR_WEBHOOK" \
         -H "Content-Type: application/json" \
         -d "{\"text\":\"🚨 ROLLBACK FAILURE: $1\",\"channel\":\"#emergency\"}"
    
    # Call emergency contacts
    echo "EMERGENCY ROLLBACK FAILED" | mail -s "CRITICAL: InmoTech Rollback Failure" emergency@inmotech.com
    
    exit 1
}

log "🚨 INITIATING EMERGENCY ROLLBACK - InmoTech Big Bang"
log "Reason: $1"
log "Started by: $(whoami) from $(hostname)"

# Step 1: Immediate Traffic Isolation (0-2 minutes)
log "🚧 Step 1/8: Isolating traffic and enabling maintenance mode"
timeout 120 bash << 'EOF' || error_exit "Failed to isolate traffic"
    # Stop accepting new connections
    sudo iptables -A INPUT -p tcp --dport 80 -j DROP
    sudo iptables -A INPUT -p tcp --dport 443 -j DROP
    
    # Enable maintenance page
    sudo ln -sf /var/www/maintenance.html /var/www/html/index.html
    
    # Stop application services
    sudo systemctl stop inmotech-backend
    sudo systemctl stop inmotech-frontend
    sudo systemctl stop nginx
    pm2 stop all
EOF
log "✅ Traffic isolated and services stopped"

# Step 2: Database Rollback (2-15 minutes)
log "🗄️ Step 2/8: Rolling back database to pre-deployment state"
timeout 900 bash << 'EOF' || error_exit "Database rollback failed"
    # Stop database connections
    sudo systemctl stop postgresql
    
    # Backup current state for forensics
    sudo cp -r /var/lib/postgresql/14/main "${BACKUP_BASE}/forensic_db_${TIMESTAMP}"
    
    # Restore from pre-deployment backup
    sudo rm -rf /var/lib/postgresql/14/main/*
    sudo -u postgres pg_ctl stop -D /var/lib/postgresql/14/main || true
    
    # Restore backup
    sudo tar -xzf "${BACKUP_BASE}/postgresql_pre_bigbang.tar.gz" -C /var/lib/postgresql/14/main/
    sudo chown -R postgres:postgres /var/lib/postgresql/14/main
    
    # Start database
    sudo systemctl start postgresql
    
    # Verify database integrity
    sudo -u postgres psql -c "SELECT version();" inmotech_db || error_exit "Database integrity check failed"
EOF
log "✅ Database rolled back successfully"

# Step 3: Application Code Rollback (15-20 minutes)
log "🔄 Step 3/8: Rolling back application code"
timeout 300 bash << 'EOF' || error_exit "Application rollback failed"
    # Backup current deployment for analysis
    sudo mv /opt/inmotech "/opt/inmotech_failed_${TIMESTAMP}"
    
    # Restore pre-deployment code
    sudo tar -xzf "${BACKUP_BASE}/inmotech_app_stable.tar.gz" -C /opt/
    sudo chown -R inmotech:inmotech /opt/inmotech
    
    # Restore environment configuration
    sudo cp "${BACKUP_BASE}/env_stable/.env" /opt/inmotech/backend/
    sudo cp "${BACKUP_BASE}/env_stable/.env" /opt/inmotech/frontend/
    
    # Restore uploads and user files
    sudo rm -rf /opt/inmotech/backend/uploads
    sudo tar -xzf "${BACKUP_BASE}/uploads_backup.tar.gz" -C /opt/inmotech/backend/
EOF
log "✅ Application code restored"

# Step 4: Configuration Rollback (20-22 minutes)
log "⚙️ Step 4/8: Restoring system configuration"
timeout 120 bash << 'EOF' || error_exit "Configuration rollback failed"
    # Restore Nginx configuration
    sudo cp "${BACKUP_BASE}/config/nginx_stable.conf" /etc/nginx/sites-available/inmotech
    sudo nginx -t || error_exit "Nginx configuration invalid"
    
    # Restore PM2 configuration
    sudo cp "${BACKUP_BASE}/config/ecosystem_stable.config.js" /opt/inmotech/backend/
    
    # Restore system services configuration
    sudo cp "${BACKUP_BASE}/config/inmotech-backend.service" /etc/systemd/system/
    sudo cp "${BACKUP_BASE}/config/inmotech-frontend.service" /etc/systemd/system/
    sudo systemctl daemon-reload
EOF
log "✅ Configuration restored"

# Step 5: Services Restart (22-25 minutes)
log "🚀 Step 5/8: Starting services with stable configuration"
timeout 180 bash << 'EOF' || error_exit "Service startup failed"
    # Start database first
    sudo systemctl start postgresql
    sleep 10
    
    # Start backend services
    cd /opt/inmotech/backend
    npm install --production --silent
    sudo systemctl start inmotech-backend
    sleep 15
    
    # Start frontend
    cd /opt/inmotech/frontend  
    npm install --production --silent
    npm run build
    sudo systemctl start inmotech-frontend
    sleep 10
    
    # Start reverse proxy
    sudo systemctl start nginx
    sleep 5
    
    # Start PM2 processes
    pm2 start /opt/inmotech/backend/ecosystem_stable.config.js
EOF
log "✅ Services started"

# Step 6: Traffic Restoration (25-27 minutes)
log "🌐 Step 6/8: Restoring traffic and removing maintenance mode"
timeout 120 bash << 'EOF' || error_exit "Traffic restoration failed"
    # Remove maintenance page
    sudo rm -f /var/www/html/index.html
    
    # Restore firewall rules
    sudo iptables -D INPUT -p tcp --dport 80 -j DROP || true
    sudo iptables -D INPUT -p tcp --dport 443 -j DROP || true
    
    # Test basic connectivity
    curl -f http://localhost/api/health || error_exit "Health check failed"
    curl -f http://localhost/ || error_exit "Frontend check failed"
EOF
log "✅ Traffic restored"

# Step 7: System Validation (27-29 minutes)
log "✅ Step 7/8: Validating system functionality"
timeout 120 bash << 'EOF' || error_exit "System validation failed"
    # Test database connectivity
    sudo -u postgres psql -c "SELECT COUNT(*) FROM users;" inmotech_db
    
    # Test API endpoints
    curl -f "http://localhost/api/properties" -H "Accept: application/json"
    curl -f "http://localhost/api/users/me" -H "Authorization: Bearer test_token" || true
    
    # Test file uploads directory
    [ -d "/opt/inmotech/backend/uploads" ] || error_exit "Uploads directory missing"
    [ -w "/opt/inmotech/backend/uploads" ] || error_exit "Uploads directory not writable"
    
    # Test frontend assets
    curl -f "http://localhost/static/js/app.js" || error_exit "Frontend assets missing"
EOF
log "✅ System validation completed"

# Step 8: Emergency Notifications (29-30 minutes)
log "📢 Step 8/8: Sending notifications and documentation"
timeout 60 bash << 'EOF' || error_exit "Notification failed"
    # Create rollback report
    cat > "/tmp/rollback_report_${TIMESTAMP}.json" << EOL
{
  "rollback_timestamp": "${TIMESTAMP}",
  "reason": "$1",
  "duration_minutes": "$(( ($(date +%s) - $(date -d "${TIMESTAMP:0:8} ${TIMESTAMP:9:2}:${TIMESTAMP:11:2}:${TIMESTAMP:13:2}" +%s)) / 60 ))",
  "success": true,
  "rolled_back_from": "BigBang deployment",
  "current_version": "stable_pre_bigbang",
  "services_status": {
    "database": "operational",
    "backend": "operational", 
    "frontend": "operational",
    "nginx": "operational"
  }
}
EOL
    
    # Send notifications
    curl -X POST "https://hooks.slack.com/services/YOUR_WEBHOOK" \
         -H "Content-Type: application/json" \
         -d "{\"text\":\"✅ EMERGENCY ROLLBACK COMPLETED\",\"attachments\":[{\"color\":\"good\",\"text\":\"System restored to stable state in $(( ($(date +%s) - $(date -d "${TIMESTAMP:0:8} ${TIMESTAMP:9:2}:${TIMESTAMP:11:2}:${TIMESTAMP:13:2}" +%s)) / 60 )) minutes\"}]}"
    
    # Email stakeholders
    echo "Emergency rollback completed successfully. System is operational." | mail -s "InmoTech: Emergency Rollback Completed" stakeholders@inmotech.com
EOF

log "🎉 EMERGENCY ROLLBACK COMPLETED SUCCESSFULLY"
log "Total time: $(( ($(date +%s) - $(date -d "${TIMESTAMP:0:8} ${TIMESTAMP:9:2}:${TIMESTAMP:11:2}:${TIMESTAMP:13:2}" +%s)) / 60 )) minutes"
log "System restored to stable pre-BigBang state"
log "Next steps: Analyze logs in /var/log/inmotech/ and plan corrective actions"

exit 0
```

---

## 🔧 Script 2: Rollback Manual Guiado (No Crítico)

```bash
#!/bin/bash
# manual_rollback_guided.sh
# Para uso cuando se necesita rollback controlado con validaciones manuales

echo "🔄 MANUAL GUIDED ROLLBACK - InmoTech"
echo "=================================="

read -p "Enter reason for rollback: " REASON
read -p "Confirm you want to proceed with rollback [yes/no]: " CONFIRM

if [[ $CONFIRM != "yes" ]]; then
    echo "Rollback cancelled by user"
    exit 1
fi

echo "Starting manual rollback process..."
echo "Reason: $REASON"

# Interactive rollback with checkpoints
echo "Step 1: Stopping services..."
sudo systemctl stop inmotech-backend inmotech-frontend nginx
read -p "Services stopped. Press Enter to continue..."

echo "Step 2: Database rollback..."
echo "Current database will be backed up to /backup/forensic/"
read -p "Proceed with database rollback? [yes/no]: " DB_CONFIRM
if [[ $DB_CONFIRM == "yes" ]]; then
    sudo -u postgres pg_dump inmotech_db > "/backup/forensic/db_before_rollback_$(date +%Y%m%d_%H%M%S).sql"
    sudo -u postgres psql inmotech_db < /backup/inmotech/pre_bigbang/db_stable.sql
    echo "✅ Database rolled back"
fi

echo "Step 3: Application rollback..."
read -p "Proceed with application rollback? [yes/no]: " APP_CONFIRM
if [[ $APP_CONFIRM == "yes" ]]; then
    sudo mv /opt/inmotech "/opt/inmotech_backup_$(date +%Y%m%d_%H%M%S)"
    sudo tar -xzf /backup/inmotech/pre_bigbang/app_stable.tar.gz -C /opt/
    echo "✅ Application rolled back"
fi

echo "Step 4: Starting services..."
read -p "Proceed to start services? [yes/no]: " START_CONFIRM
if [[ $START_CONFIRM == "yes" ]]; then
    sudo systemctl start postgresql
    sleep 10
    sudo systemctl start inmotech-backend
    sleep 10  
    sudo systemctl start inmotech-frontend
    sudo systemctl start nginx
    echo "✅ Services started"
fi

echo "Step 5: Validation..."
echo "Testing system functionality..."
curl -f http://localhost/api/health && echo "✅ Backend OK" || echo "❌ Backend FAILED"
curl -f http://localhost/ && echo "✅ Frontend OK" || echo "❌ Frontend FAILED"

echo "🎉 Manual rollback completed"
echo "Please verify full system functionality before confirming success"
```

---

## 📊 Criterios de Activación de Rollback

### 🚨 Activación Automática (Script ejecuta solo)

| Condición | Umbral | Tiempo de Gracia |
|-----------|--------|------------------|
| **System Downtime** | >15 minutos | 5 min |
| **API Response Time** | >5000ms por >10 min | 2 min |
| **Database Errors** | >100 errores/min | 1 min |
| **Memory Usage** | >95% por >5 min | 3 min |
| **Disk Space** | <5% disponible | 1 min |

### 🔧 Activación Manual (Requiere decisión)

| Situación | Responsable | Tiempo Máximo de Decisión |
|-----------|-------------|---------------------------|
| **User Complaints** | Product Manager | 30 minutos |
| **Data Inconsistency** | Data Team Lead | 15 minutos |  
| **Security Breach** | Security Officer | 5 minutos |
| **Business Impact** | C-Level | 20 minutos |
| **External Dependencies** | Integration Lead | 45 minutos |

---

## 🗄️ Backup Strategy para Rollback

### 📁 Estructura de Backups Pre-Despliegue

```
/backup/inmotech/pre_bigbang/
├── postgresql_pre_bigbang.tar.gz        # Full DB backup
├── inmotech_app_stable.tar.gz           # Complete application
├── uploads_backup.tar.gz                # User files and uploads
├── config/                              # All configurations
│   ├── nginx_stable.conf
│   ├── ecosystem_stable.config.js
│   ├── inmotech-backend.service
│   └── inmotech-frontend.service
├── env_stable/                          # Environment files
│   └── .env                            
└── verification/                        # Integrity checksums
    ├── postgresql.md5
    ├── application.md5
    └── uploads.md5
```

### 🔐 Validación de Integridad de Backups

```bash
# Ejecutar antes del despliegue para verificar backups
#!/bin/bash
echo "🔍 Validating backup integrity for rollback..."

BACKUP_BASE="/backup/inmotech/pre_bigbang"

# Verify checksums
cd "$BACKUP_BASE"
md5sum -c verification/postgresql.md5 || echo "❌ Database backup corrupted"
md5sum -c verification/application.md5 || echo "❌ Application backup corrupted"  
md5sum -c verification/uploads.md5 || echo "❌ Uploads backup corrupted"

# Test database backup
echo "Testing database backup restore..."
sudo -u postgres pg_restore --list postgresql_pre_bigbang.tar.gz > /dev/null && echo "✅ DB backup valid"

# Test application backup
echo "Testing application backup..."
tar -tzf inmotech_app_stable.tar.gz > /dev/null && echo "✅ App backup valid"

echo "🎉 All backups validated for rollback"
```

---

## 📞 Plan de Comunicación Durante Rollback

### 🚨 Comunicación Automática

**Slack Notifications:**
```json
{
  "trigger": "Rollback iniciado",
  "channel": "#emergency",
  "message": "🚨 ROLLBACK EN PROGRESO - InmoTech BigBang",
  "mentions": ["@channel", "@devops-team"],
  "updates": "Cada 5 minutos"
}
```

**Email Alerts:**
```yaml
Recipients:
  - emergency@inmotech.com
  - cto@inmotech.com
  - devops@inmotech.com
  
Subject: "🚨 CRITICAL: InmoTech Emergency Rollback in Progress"
Frequency: "Initial + completion notification"
```

### 📢 Comunicación Externa (Si es necesario)

**Para Stakeholders:**
```text
ASUNTO: Mantenimiento de Emergencia - InmoTech

Estimados partners,

Estamos realizando un mantenimiento de emergencia en nuestros sistemas. 
El servicio será restaurado en aproximadamente 30 minutos.

Disculpen las molestias.

Equipo InmoTech
```

**Para Usuarios Finales:**
```html
<!-- Página de mantenimiento -->
<!DOCTYPE html>
<html>
<head>
    <title>InmoTech - Mantenimiento</title>
</head>
<body>
    <h1>🔧 Sistema en Mantenimiento</h1>
    <p>Estamos solucionando un problema técnico.</p>
    <p>El servicio se restaurará en breve.</p>
    <p>Gracias por su paciencia.</p>
    <!-- Auto refresh cada 5 minutos -->
    <meta http-equiv="refresh" content="300">
</body>
</html>
```

---

## ✅ Checklist de Post-Rollback

### 🔍 Validación Inmediata (0-15 min post-rollback)

- [ ] ✅ **Sistema operativo:** Todos los servicios running
- [ ] ✅ **Base de datos:** Conexiones funcionales y queries básicas
- [ ] ✅ **API Backend:** Endpoints principales respondiendo  
- [ ] ✅ **Frontend:** Página principal carga correctamente
- [ ] ✅ **Autenticación:** Login/logout funcional
- [ ] ✅ **Uploads:** Directorio de archivos accesible

### 🧪 Testing Funcional (15-60 min post-rollback)

- [ ] ✅ **Flujo usuarios:** Registro/login/navegación básica
- [ ] ✅ **Gestión propiedades:** CRUD operations básicas
- [ ] ✅ **Chat/mensajería:** Sistema de comunicación
- [ ] ✅ **Notificaciones:** Push/email funcionando
- [ ] ✅ **Archivos:** Upload/download de documentos
- [ ] ✅ **Ofertas:** Crear/ver/gestionar ofertas

### 📊 Monitoreo Extendido (1-24 horas post-rollback)

- [ ] ✅ **Performance:** Tiempos de respuesta normales
- [ ] ✅ **Logs:** Sin errores críticos en logs
- [ ] ✅ **Recursos:** CPU/memoria/disco en niveles normales
- [ ] ✅ **Usuarios:** Feedback de usuarios normales
- [ ] ✅ **Integraciones:** APIs externas funcionando
- [ ] ✅ **Seguridad:** Sin alertas de seguridad

---

## 🔄 Procedimiento de Re-intento Post-Rollback

### 📋 Análisis de Causa Raíz (Obligatorio antes de re-intento)

```yaml
Root Cause Analysis Required:
  1. Technical Analysis:
     - Review deployment logs
     - Identify exact failure point
     - Determine if issue is fixable
     
  2. Risk Assessment:
     - Evaluate probability of success
     - Consider alternative approaches
     - Review timeline constraints
     
  3. Stakeholder Approval:
     - Present findings to leadership
     - Get explicit approval for re-attempt
     - Define success/failure criteria
```

### 🛠️ Preparación para Re-intento

**Antes de nuevo intento:**
- [ ] Causa raíz identificada y corregida
- [ ] Solución validada en ambiente de testing
- [ ] Rollback procedures probados nuevamente
- [ ] Stakeholders informados y de acuerdo
- [ ] Infraestructura revisada y optimizada
- [ ] Team postmortem completado

---

## 📈 Métricas de Éxito del Rollback

### ⏱️ Objetivos Temporales

| Fase | Tiempo Objetivo | Tiempo Máximo |
|------|----------------|---------------|
| **Decisión de rollback** | < 5 min | < 15 min |
| **Ejecución completa** | < 25 min | < 45 min |
| **Validación básica** | < 30 min | < 60 min |
| **Sistema estable** | < 45 min | < 90 min |

### 📊 KPIs de Calidad del Rollback

| Métrica | Target | Crítico |
|---------|--------|---------|
| **Data Integrity** | 100% | >99.9% |
| **Service Availability** | >99% | >95% |
| **User Impact Duration** | <30 min | <60 min |
| **Rollback Success Rate** | 100% | >95% |

---

## 🚨 Contactos de Emergencia

### 📞 Emergency Response Team (24/7)

| Rol | Contacto Principal | Backup | Escalation |
|-----|-------------------|--------|------------|
| **Rollback Lead** | +34 XXX XXX XXX | +34 YYY YYY YYY | CTO |
| **Database Admin** | +34 XXX XXX XXX | +34 YYY YYY YYY | Sr. DBA |
| **DevOps Engineer** | +34 XXX XXX XXX | +34 YYY YYY YYY | DevOps Manager |
| **Security Officer** | +34 XXX XXX XXX | +34 YYY YYY YYY | CISO |
| **Business Continuity** | +34 XXX XXX XXX | +34 YYY YYY YYY | COO |

### 📧 Email Lists

- **emergency@inmotech.com** - Immediate response team
- **stakeholders@inmotech.com** - Business stakeholders
- **devops@inmotech.com** - Technical team
- **support@inmotech.com** - Customer support team

---

**Plan aprobado por:**  
**Emergency Response Team - InmoTech**  
**Fecha:** 21 de Noviembre 2025  
**Versión:** 1.0 - CRÍTICO  
**Próxima Revisión:** Pre-despliegue validación