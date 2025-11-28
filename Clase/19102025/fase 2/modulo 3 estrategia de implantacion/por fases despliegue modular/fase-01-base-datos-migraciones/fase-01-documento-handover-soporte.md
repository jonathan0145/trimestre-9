# Documento de Handover y Soporte - Fase 1: Base de Datos y Migraciones

## 📋 Información del Proyecto
- **Proyecto:** InmoTech - Sistema de Gestión Inmobiliaria
- **Fase:** Fase 1 - Base de Datos y Migraciones
- **Fecha de Handover:** 10/01/2026
- **Equipo de Desarrollo:** Carlos Martínez, Ana García, Miguel Torres, Laura Pérez
- **Equipo de Soporte:** Equipo de Operaciones InmoTech
- **Versión:** 1.0

---

## 🎯 Objetivo del Traspaso

### Objetivo Principal
Transferir de forma completa y estructurada la responsabilidad de soporte y mantenimiento de la Fase 1 (Base de Datos y Migraciones) del sistema InmoTech desde el equipo de desarrollo al equipo de operaciones, asegurando continuidad operacional sin interrupciones.

### Objetivos Específicos
- ✅ Documentar todos los componentes implementados en Fase 1
- ✅ Transferir conocimiento técnico completo al equipo de soporte
- ✅ Establecer procedimientos de soporte y escalación
- ✅ Configurar monitoreo y alertas para operaciones 24/7
- ✅ Validar competencias del equipo de soporte
- ✅ Definir SLAs y métricas de soporte

---

## 📦 INVENTARIO DE COMPONENTES ENTREGADOS

### 1. Base de Datos PostgreSQL 14

#### ✅ Configuración Principal
- **Servidor:** PostgreSQL 14.10 (Ubuntu 22.04 LTS)
- **Puerto:** 5433 (no estándar por seguridad)
- **Base de Datos:** `inmotech`
- **Charset:** UTF8
- **Collation:** en_US.UTF-8
- **Timezone:** Europe/Madrid

#### ✅ Estructura de Datos Implementada
```sql
-- Resumen de tablas principales
SELECT 
    schemaname,
    tablename,
    tableowner,
    tablespace,
    hasindexes,
    hasrules,
    hastriggers
FROM pg_tables 
WHERE schemaname = 'public'
ORDER BY tablename;

-- Resultado esperado:
-- 8 tablas: roles, users, properties, transactions, messages, notifications, files, offers
```

**Tablas Principales:**
1. **roles (5 registros)** - Roles de usuario del sistema
2. **users (100 registros)** - Usuarios del sistema con diferentes roles  
3. **properties (500 registros)** - Catálogo de propiedades inmobiliarias
4. **transactions (150 registros)** - Transacciones completadas
5. **messages (1000 registros)** - Sistema de mensajería interna
6. **notifications (800 registros)** - Sistema de notificaciones
7. **files (1200 registros)** - Metadatos de archivos del sistema
8. **offers (300 registros)** - Ofertas sobre propiedades

#### ✅ Usuarios y Permisos
```sql
-- Usuarios creados para operación
SELECT 
    usename as usuario,
    usesuper as admin,
    usecreatedb as puede_crear_db,
    usecreatereole as puede_crear_roles,
    valuntil as expira
FROM pg_user
WHERE usename LIKE 'inmotech%' OR usename = 'postgres'
ORDER BY usename;
```

**Cuentas Configuradas:**
- `postgres` - Superusuario (solo emergencias)
- `inmotech_admin` - Administración BD (DDL/DML completo)
- `inmotech_app` - Usuario aplicación (DML limitado)
- `inmotech_readonly` - Solo lectura (reportes/monitoreo)
- `inmotech_backup` - Usuario para backups automatizados

### 2. Índices y Optimizaciones

#### ✅ Índices Creados (Total: 23 índices)
```sql
-- Verificar todos los índices
SELECT 
    schemaname,
    tablename,
    indexname,
    indexdef
FROM pg_indexes 
WHERE schemaname = 'public'
ORDER BY tablename, indexname;
```

**Índices de Performance Críticos:**
- `idx_users_email` - Búsqueda rápida por email
- `idx_properties_type_status` - Filtros principales de propiedades
- `idx_properties_coordinates` - Búsquedas geoespaciales (GIST)
- `idx_messages_created_at` - Timeline de mensajes
- `idx_transactions_date_status` - Reportes financieros

### 3. Sistema de Auditoría

#### ✅ Componentes de Auditoría
```sql
-- Verificar sistema de auditoría
SELECT 
    tablename,
    trigger_name,
    event_manipulation,
    action_statement
FROM information_schema.triggers 
WHERE trigger_schema = 'public'
ORDER BY tablename;
```

**Triggers de Auditoría Activos:**
- `audit_users_trigger` - Cambios en tabla users
- `audit_properties_trigger` - Cambios en propiedades
- `audit_transactions_trigger` - Transacciones financieras

**Tabla de Auditoría:** `audit_log`
- Retención: 24 meses
- Promedio: 500 eventos/día
- Alertas: Configuradas para eventos críticos

### 4. Backups Automatizados

#### ✅ Configuración de Backup
```bash
# Verificar configuración de backup
crontab -l | grep postgres

# Resultado esperado:
# 0 2 * * * /scripts/backup_inmotech.sh
# 0 8,14,20 * * * /scripts/backup_incremental.sh
```

**Estrategia de Backup:**
- **Backup completo:** Diario a las 02:00 AM
- **Backup incremental:** 3 veces al día (08:00, 14:00, 20:00)
- **Cifrado:** AES-256 con GPG
- **Retención:** 30 días locales, 12 meses offsite
- **Ubicación:** `/backups/postgresql/` (local), AWS S3 (offsite)

### 5. Monitoreo y Alertas

#### ✅ Métricas Configuradas
```sql
-- Verificar extensiones de monitoreo
SELECT extname, extversion 
FROM pg_extension
WHERE extname IN ('pg_stat_statements', 'postgres_fdw');
```

**Dashboards Configurados:**
- **Grafana Dashboard:** PostgreSQL Performance (puerto 3000)
- **Prometheus Metrics:** postgres_exporter (puerto 9187)
- **Custom Dashboard:** InmoTech DB Metrics (puerto 3001)

**Alertas Activas:**
- CPU > 80% por 5 minutos
- Conexiones > 150 (75% del máximo)
- Queries lentas > 10 segundos
- Disco disponible < 20%
- Fallos de backup

---

## 📚 DOCUMENTACIÓN DE SOPORTE

### 1. Procedimientos Operativos Estándar (SOPs)

#### 🔧 SOP-001: Monitoreo Diario de la Base de Datos

**Frecuencia:** Diario, 08:00 AM  
**Responsable:** Operador de turno  
**Duración estimada:** 15 minutos

**Procedimiento:**
```bash
#!/bin/bash
# Archivo: /scripts/daily_health_check.sh

echo "=== CHEQUEO DIARIO BD INMOTECH $(date) ==="

# 1. Verificar estado del servicio PostgreSQL
systemctl status postgresql | grep "active (running)"
if [ $? -eq 0 ]; then
    echo "✅ PostgreSQL service: RUNNING"
else
    echo "❌ PostgreSQL service: DOWN - ESCALATE IMMEDIATELY"
    exit 1
fi

# 2. Verificar conectividad
psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -c "SELECT 'DB Connection: OK';" >/dev/null 2>&1
if [ $? -eq 0 ]; then
    echo "✅ Database connectivity: OK"
else
    echo "❌ Database connectivity: FAILED - CHECK NETWORK/AUTH"
    exit 1
fi

# 3. Verificar uso de espacio en disco
DISK_USAGE=$(df /var/lib/postgresql | tail -1 | awk '{print $5}' | sed 's/%//')
if [ $DISK_USAGE -lt 80 ]; then
    echo "✅ Disk usage: ${DISK_USAGE}% (OK)"
else
    echo "⚠️ Disk usage: ${DISK_USAGE}% (WARNING - Consider cleanup)"
fi

# 4. Verificar conexiones activas
ACTIVE_CONNECTIONS=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "SELECT count(*) FROM pg_stat_activity WHERE state = 'active';")
MAX_CONNECTIONS=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "SELECT setting::int FROM pg_settings WHERE name = 'max_connections';")
CONNECTION_PERCENTAGE=$(( ACTIVE_CONNECTIONS * 100 / MAX_CONNECTIONS ))

if [ $CONNECTION_PERCENTAGE -lt 70 ]; then
    echo "✅ Active connections: ${ACTIVE_CONNECTIONS}/${MAX_CONNECTIONS} (${CONNECTION_PERCENTAGE}%)"
else
    echo "⚠️ Active connections: ${ACTIVE_CONNECTIONS}/${MAX_CONNECTIONS} (${CONNECTION_PERCENTAGE}% - Monitor closely)"
fi

# 5. Verificar queries lentas
SLOW_QUERIES=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT count(*) 
    FROM pg_stat_activity 
    WHERE state = 'active' 
    AND query_start < now() - interval '30 seconds'
    AND query NOT LIKE '%pg_stat_activity%';
")

if [ $SLOW_QUERIES -eq 0 ]; then
    echo "✅ Slow queries: None detected"
else
    echo "⚠️ Slow queries: ${SLOW_QUERIES} queries running > 30 seconds"
fi

# 6. Verificar último backup
LAST_BACKUP=$(find /backups/postgresql -name "*.gpg" -type f -mtime -1 | wc -l)
if [ $LAST_BACKUP -gt 0 ]; then
    echo "✅ Recent backup: Found backup within last 24h"
else
    echo "❌ Recent backup: NO BACKUP FOUND - ESCALATE"
fi

# 7. Verificar logs de error recientes
ERROR_COUNT=$(grep -c "ERROR\|FATAL\|PANIC" /var/log/postgresql/postgresql-*.log 2>/dev/null | tail -1)
if [ $ERROR_COUNT -lt 5 ]; then
    echo "✅ Error logs: ${ERROR_COUNT} errors in last log file (acceptable)"
else
    echo "⚠️ Error logs: ${ERROR_COUNT} errors detected - Review logs"
fi

echo "=== CHEQUEO DIARIO COMPLETADO ==="
```

**Acciones en caso de alertas:**
- ✅ **Todo OK:** Documentar en log de operaciones
- ⚠️ **Warning:** Monitorear cada hora, preparar escalación
- ❌ **Error:** Escalación inmediata según matriz de escalación

#### 🔧 SOP-002: Backup y Restore

**Backup Manual (Emergencia):**
```bash
#!/bin/bash
# Archivo: /scripts/emergency_backup.sh

EMERGENCY_DIR="/backups/emergency/$(date +%Y%m%d_%H%M%S)"
mkdir -p "$EMERGENCY_DIR"

echo "=== BACKUP DE EMERGENCIA INICIADO ==="

# Backup completo
pg_dump -h localhost -p 5433 -U postgres -d inmotech \
    --clean --create --verbose \
    --file="$EMERGENCY_DIR/inmotech_emergency.sql"

if [ $? -eq 0 ]; then
    echo "✅ Backup completado en: $EMERGENCY_DIR"
    
    # Cifrar inmediatamente
    gpg --symmetric --cipher-algo AES256 \
        --output "$EMERGENCY_DIR/inmotech_emergency.sql.gpg" \
        "$EMERGENCY_DIR/inmotech_emergency.sql"
    
    # Limpiar archivo sin cifrar
    rm "$EMERGENCY_DIR/inmotech_emergency.sql"
    
    echo "✅ Backup cifrado y asegurado"
else
    echo "❌ BACKUP FAILED - ESCALATE IMMEDIATELY"
    exit 1
fi
```

**Restore Procedure:**
```bash
#!/bin/bash
# Archivo: /scripts/restore_procedure.sh

BACKUP_FILE="$1"

if [ -z "$BACKUP_FILE" ]; then
    echo "Uso: $0 <archivo_backup.sql.gpg>"
    echo "Backups disponibles:"
    ls -la /backups/postgresql/*.gpg | tail -5
    exit 1
fi

echo "=== PROCEDIMIENTO DE RESTORE ==="
echo "⚠️ ESTA OPERACIÓN REEMPLAZARÁ TODOS LOS DATOS"
echo "Backup a restaurar: $BACKUP_FILE"
read -p "¿Continuar? (escriba 'YES' para confirmar): " CONFIRM

if [ "$CONFIRM" != "YES" ]; then
    echo "Operación cancelada"
    exit 0
fi

# 1. Crear backup de seguridad del estado actual
echo "Paso 1: Backup de seguridad pre-restore"
/scripts/emergency_backup.sh

# 2. Desconectar usuarios activos
echo "Paso 2: Desconectando usuarios"
psql -h localhost -p 5433 -U postgres -d inmotech -c "
    SELECT pg_terminate_backend(pid) 
    FROM pg_stat_activity 
    WHERE datname = 'inmotech' AND pid <> pg_backend_pid();
"

# 3. Descifrar y restaurar
echo "Paso 3: Restaurando datos"
gpg --quiet --decrypt "$BACKUP_FILE" | psql -h localhost -p 5433 -U postgres -d postgres

if [ $? -eq 0 ]; then
    echo "✅ RESTORE COMPLETADO EXITOSAMENTE"
    echo "Verificar integridad con: /scripts/post_restore_validation.sh"
else
    echo "❌ RESTORE FAILED - REVISAR LOGS INMEDIATAMENTE"
    exit 1
fi
```

#### 🔧 SOP-003: Troubleshooting Conexiones

**Diagnóstico de Problemas de Conexión:**
```bash
#!/bin/bash
# Archivo: /scripts/connection_troubleshoot.sh

echo "=== DIAGNÓSTICO DE CONEXIONES ==="

# 1. Verificar estado del servicio
echo "1. Estado del servicio PostgreSQL:"
systemctl status postgresql --no-pager

# 2. Verificar puerto en escucha
echo -e "\n2. Puerto PostgreSQL en escucha:"
netstat -tlnp | grep :5433

# 3. Verificar procesos PostgreSQL
echo -e "\n3. Procesos PostgreSQL:"
ps aux | grep postgres | grep -v grep

# 4. Verificar conexiones activas por usuario
echo -e "\n4. Conexiones por usuario:"
psql -h localhost -p 5433 -U postgres -d inmotech -c "
    SELECT 
        usename as usuario,
        count(*) as conexiones,
        string_agg(DISTINCT state, ', ') as estados,
        string_agg(DISTINCT application_name, ', ') as aplicaciones
    FROM pg_stat_activity 
    WHERE datname = 'inmotech'
    GROUP BY usename
    ORDER BY conexiones DESC;
" 2>/dev/null || echo "❌ No se pudo conectar a la BD"

# 5. Verificar límites de conexión
echo -e "\n5. Límites de conexión:"
psql -h localhost -p 5433 -U postgres -d inmotech -c "
    SELECT 
        'max_connections' as setting,
        setting as value
    FROM pg_settings 
    WHERE name = 'max_connections'
    UNION ALL
    SELECT 
        'current_connections',
        count(*)::text
    FROM pg_stat_activity;
" 2>/dev/null

# 6. Verificar logs de errores recientes
echo -e "\n6. Errores recientes en logs:"
tail -20 /var/log/postgresql/postgresql-*.log | grep -E "ERROR|FATAL|connection"

# 7. Test de conectividad básico
echo -e "\n7. Test de conectividad:"
for user in postgres inmotech_app inmotech_readonly; do
    psql -h localhost -p 5433 -U "$user" -d inmotech -c "SELECT 'OK' as test;" >/dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo "✅ Usuario $user: Conexión OK"
    else
        echo "❌ Usuario $user: Error de conexión"
    fi
done

echo -e "\n=== DIAGNÓSTICO COMPLETADO ==="
```

### 2. Matriz de Escalación

#### 🚨 Niveles de Severidad

**CRÍTICO (Sev 1) - Tiempo de Respuesta: 15 minutos**
- Base de datos completamente inaccesible
- Pérdida de datos detectada
- Corrupción de datos críticos
- Brecha de seguridad confirmada

**ALTO (Sev 2) - Tiempo de Respuesta: 1 hora**
- Rendimiento degradado > 50%
- Funcionalidades principales indisponibles
- Backup fallido por más de 24h
- Errores en aplicación debido a BD

**MEDIO (Sev 3) - Tiempo de Respuesta: 4 horas**
- Rendimiento degradado < 50%
- Funcionalidades secundarias afectadas
- Alertas de monitoreo persistentes
- Necesidad de optimización urgente

**BAJO (Sev 4) - Tiempo de Respuesta: 24 horas**
- Consultas de información
- Optimizaciones no urgentes
- Actualizaciones de documentación
- Capacitación y transferencia de conocimiento

#### 👥 Contactos de Escalación

**Nivel 1: Operaciones (24/7)**
- **Contacto Principal:** Operador de Turno
- **Teléfono:** +34 900-123-456
- **Email:** ops@inmotech.com
- **Slack:** #inmotech-ops

**Nivel 2: DBA Senior (Horario Laboral + Guardia)**
- **Nombre:** Carlos Martínez
- **Teléfono:** +34 600-789-123
- **Email:** carlos.martinez@inmotech.com
- **Slack:** @carlos.martinez
- **Respaldo:** Ana García (+34 600-789-124)

**Nivel 3: Liderazgo Técnico**
- **Líder DevOps:** Miguel Torres (+34 600-789-125)
- **CTO:** Director Técnico (+34 600-789-999)

**Nivel 4: Ejecutivo**
- **CEO:** CEO InmoTech (+34 600-000-001)

#### 📞 Procedimiento de Escalación
```bash
#!/bin/bash
# Archivo: /scripts/escalate_incident.sh

SEVERITY="$1"
ISSUE_DESCRIPTION="$2"
INCIDENT_ID="INC-$(date +%Y%m%d_%H%M%S)"

if [ -z "$SEVERITY" ] || [ -z "$ISSUE_DESCRIPTION" ]; then
    echo "Uso: $0 <SEV1|SEV2|SEV3|SEV4> '<descripción del incidente>'"
    exit 1
fi

echo "=== ESCALANDO INCIDENTE ==="
echo "Incident ID: $INCIDENT_ID"
echo "Severity: $SEVERITY"
echo "Description: $ISSUE_DESCRIPTION"
echo "Timestamp: $(date)"

case "$SEVERITY" in
    "SEV1")
        # Escalación crítica - Notificar a todos inmediatamente
        SUBJECT="CRITICAL: InmoTech DB Issue - $INCIDENT_ID"
        RECIPIENTS="ops@inmotech.com carlos.martinez@inmotech.com miguel.torres@inmotech.com"
        
        # SMS para criticales (si está configurado)
        # send_sms "+34600789123" "CRITICAL DB Issue $INCIDENT_ID - Check email immediately"
        ;;
    "SEV2")
        # Escalación alta - DBA y Ops
        SUBJECT="HIGH: InmoTech DB Issue - $INCIDENT_ID"
        RECIPIENTS="ops@inmotech.com carlos.martinez@inmotech.com"
        ;;
    "SEV3")
        # Escalación media - Solo Ops inicial
        SUBJECT="MEDIUM: InmoTech DB Issue - $INCIDENT_ID"
        RECIPIENTS="ops@inmotech.com"
        ;;
    "SEV4")
        # Escalación baja - Ticket normal
        SUBJECT="LOW: InmoTech DB Issue - $INCIDENT_ID"
        RECIPIENTS="ops@inmotech.com"
        ;;
esac

# Crear mensaje detallado
MESSAGE="
INCIDENT DETAILS:
================
Incident ID: $INCIDENT_ID
Severity: $SEVERITY
Timestamp: $(date)
Server: $(hostname)
Database: inmotech

Issue Description:
$ISSUE_DESCRIPTION

Current System Status:
- PostgreSQL Service: $(systemctl is-active postgresql)
- Database Connectivity: $(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -c "SELECT 'OK';" 2>/dev/null || echo "FAILED")
- Disk Usage: $(df /var/lib/postgresql | tail -1 | awk '{print $5}')
- Active Connections: $(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "SELECT count(*) FROM pg_stat_activity;" 2>/dev/null || echo "N/A")

Next Steps:
1. Acknowledge this incident within SLA timeframes
2. Begin troubleshooting procedures
3. Update incident status every 30 minutes for SEV1/SEV2
4. Notify stakeholders of resolution

Incident Dashboard: http://monitoring.inmotech.com/incidents/$INCIDENT_ID
"

# Enviar notificación
echo "$MESSAGE" | mail -s "$SUBJECT" $RECIPIENTS

# Log del incidente
echo "$(date) - $INCIDENT_ID - $SEVERITY - $ISSUE_DESCRIPTION" >> /var/log/incidents.log

# Crear ticket en sistema de tracking (si existe)
# create_ticket.sh "$INCIDENT_ID" "$SEVERITY" "$ISSUE_DESCRIPTION"

echo "✅ Incident $INCIDENT_ID escalated successfully"
echo "Recipients notified: $RECIPIENTS"
```

---

## 📊 SLAS Y MÉTRICAS DE SOPORTE

### 1. Service Level Agreements (SLAs)

#### 🎯 Availability SLAs
- **Uptime objetivo:** 99.9% (8.77 horas downtime/año máximo)
- **Horario crítico:** L-V 08:00-20:00 CET
- **Maintenance window:** Sábados 02:00-06:00 CET
- **Recovery Time Objective (RTO):** 4 horas máximo
- **Recovery Point Objective (RPO):** 1 hora máximo de datos

#### ⚡ Performance SLAs
- **Query response time:** < 2 segundos (95% percentil)
- **Connection establishment:** < 5 segundos
- **Backup completion:** < 30 minutos
- **Concurrent connections:** Soporte para 150 conexiones simultáneas
- **Throughput:** 1000 transacciones/minuto

#### 🚨 Response Time SLAs
| Severidad | Primera Respuesta | Resolución Target | Escalación |
|-----------|------------------|------------------|-------------|
| SEV1 (Crítico) | 15 minutos | 4 horas | Automática después 1 hora |
| SEV2 (Alto) | 1 hora | 8 horas | Manual después 4 horas |
| SEV3 (Medio) | 4 horas | 24 horas | Manual después 12 horas |
| SEV4 (Bajo) | 24 horas | 72 horas | N/A |

### 2. Métricas de Monitoreo Continuo

#### 📈 Dashboard Principal - KPIs Críticos
```sql
-- Query para dashboard de métricas en tiempo real
-- Archivo: /scripts/realtime_metrics.sql

-- Conexiones activas vs máximas
SELECT 
    'active_connections' as metric,
    COUNT(*) as current_value,
    (SELECT setting::int FROM pg_settings WHERE name = 'max_connections') as max_value,
    ROUND(COUNT(*) * 100.0 / (SELECT setting::int FROM pg_settings WHERE name = 'max_connections'), 1) as percentage
FROM pg_stat_activity 
WHERE state = 'active';

-- Queries lentas (> 5 segundos)
SELECT 
    'slow_queries' as metric,
    COUNT(*) as current_value,
    5 as threshold_seconds
FROM pg_stat_activity 
WHERE state = 'active' 
AND query_start < now() - interval '5 seconds'
AND query NOT LIKE '%pg_stat_activity%';

-- Tamaño de base de datos
SELECT 
    'database_size_mb' as metric,
    ROUND(pg_database_size('inmotech') / 1024 / 1024, 2) as current_value_mb;

-- Transacciones por minuto (últimos 5 minutos)
SELECT 
    'transactions_per_minute' as metric,
    ROUND(
        (xact_commit + xact_rollback) / 
        EXTRACT(epoch FROM (now() - stats_reset)) * 60, 2
    ) as current_value
FROM pg_stat_database 
WHERE datname = 'inmotech';

-- Cache hit ratio
SELECT 
    'cache_hit_ratio' as metric,
    ROUND(
        100 * sum(blks_hit) / (sum(blks_hit) + sum(blks_read) + 1), 2
    ) as percentage
FROM pg_stat_database 
WHERE datname = 'inmotech';

-- Deadlocks en últimas 24 horas
SELECT 
    'deadlocks_24h' as metric,
    deadlocks as current_value
FROM pg_stat_database 
WHERE datname = 'inmotech';

-- Espacio en disco disponible
SELECT 
    'disk_usage_percentage' as metric,
    ROUND(
        100 - (
            SELECT CAST(regexp_replace(
                (SELECT pg_size_pretty(pg_database_size('inmotech'))), 
                '[^0-9]', '', 'g'
            ) AS numeric) * 100.0 / 1000000
        ), 1
    ) as available_percentage;
```

#### 📊 Métricas de Rendimiento (Colección cada 5 minutos)
```bash
#!/bin/bash
# Archivo: /scripts/collect_performance_metrics.sh

METRICS_LOG="/var/log/inmotech_metrics.log"
TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')

# Función para registrar métrica
log_metric() {
    local metric_name="$1"
    local metric_value="$2"
    echo "$TIMESTAMP,$metric_name,$metric_value" >> "$METRICS_LOG"
}

# 1. CPU y memoria del proceso PostgreSQL
PG_PID=$(pgrep -f "postgres.*main")
PG_CPU=$(ps -p $PG_PID -o %cpu --no-headers 2>/dev/null | tr -d ' ')
PG_MEM=$(ps -p $PG_PID -o %mem --no-headers 2>/dev/null | tr -d ' ')

log_metric "postgresql_cpu_percentage" "$PG_CPU"
log_metric "postgresql_memory_percentage" "$PG_MEM"

# 2. Número de conexiones por estado
ACTIVE_CONN=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT count(*) FROM pg_stat_activity WHERE state = 'active';
" 2>/dev/null)

IDLE_CONN=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT count(*) FROM pg_stat_activity WHERE state = 'idle';
" 2>/dev/null)

log_metric "connections_active" "$ACTIVE_CONN"
log_metric "connections_idle" "$IDLE_CONN"

# 3. Tiempo promedio de queries
AVG_QUERY_TIME=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT COALESCE(ROUND(AVG(EXTRACT(epoch FROM (now() - query_start))), 2), 0)
    FROM pg_stat_activity 
    WHERE state = 'active' AND query_start IS NOT NULL;
" 2>/dev/null)

log_metric "avg_query_time_seconds" "$AVG_QUERY_TIME"

# 4. Uso de disco de la BD
DB_SIZE_MB=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT ROUND(pg_database_size('inmotech') / 1024 / 1024, 2);
" 2>/dev/null)

log_metric "database_size_mb" "$DB_SIZE_MB"

# 5. Espacio disponible en disco
DISK_AVAILABLE=$(df /var/lib/postgresql | tail -1 | awk '{print $4}')
log_metric "disk_available_kb" "$DISK_AVAILABLE"

# 6. Rate de transacciones
TXN_RATE=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT COALESCE(xact_commit + xact_rollback, 0) 
    FROM pg_stat_database 
    WHERE datname = 'inmotech';
" 2>/dev/null)

log_metric "total_transactions" "$TXN_RATE"

# 7. Número de errores en logs (última hora)
ERROR_COUNT=$(grep -c "ERROR\|FATAL" /var/log/postgresql/postgresql-*.log | tail -1 | cut -d: -f2)
log_metric "errors_last_hour" "${ERROR_COUNT:-0}"
```

### 3. Reportes Operacionales

#### 📋 Reporte Diario Automático
```bash
#!/bin/bash
# Archivo: /scripts/daily_operations_report.sh

REPORT_DATE=$(date '+%Y-%m-%d')
REPORT_FILE="/reports/inmotech_daily_$REPORT_DATE.html"

cat > "$REPORT_FILE" << EOF
<!DOCTYPE html>
<html>
<head>
    <title>InmoTech Database Daily Operations Report - $REPORT_DATE</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .header { background-color: #2c3e50; color: white; padding: 20px; text-align: center; }
        .metric { margin: 10px 0; padding: 10px; border-left: 4px solid #3498db; }
        .ok { border-left-color: #27ae60; }
        .warning { border-left-color: #f39c12; }
        .error { border-left-color: #e74c3c; }
        table { width: 100%; border-collapse: collapse; margin: 20px 0; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
    </style>
</head>
<body>
    <div class="header">
        <h1>InmoTech Database Operations Report</h1>
        <h2>$REPORT_DATE</h2>
    </div>
EOF

# Resumen ejecutivo
echo "<h2>Executive Summary</h2>" >> "$REPORT_FILE"

# Obtener métricas clave
UPTIME=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT date_trunc('seconds', now() - pg_postmaster_start_time());
" 2>/dev/null)

TOTAL_CONNECTIONS=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT count(*) FROM pg_stat_activity;
" 2>/dev/null)

AVG_RESPONSE_TIME=$(psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT ROUND(AVG(mean_time), 2) FROM pg_stat_statements LIMIT 1;
" 2>/dev/null)

echo "<div class='metric ok'><strong>Database Uptime:</strong> $UPTIME</div>" >> "$REPORT_FILE"
echo "<div class='metric ok'><strong>Active Connections:</strong> $TOTAL_CONNECTIONS</div>" >> "$REPORT_FILE"
echo "<div class='metric ok'><strong>Average Response Time:</strong> ${AVG_RESPONSE_TIME:-N/A} ms</div>" >> "$REPORT_FILE"

# Tabla de actividad por horas
echo "<h2>Activity by Hour (Last 24h)</h2>" >> "$REPORT_FILE"
echo "<table>" >> "$REPORT_FILE"
echo "<tr><th>Hour</th><th>Connections</th><th>Queries</th><th>Avg Response (ms)</th></tr>" >> "$REPORT_FILE"

for hour in {0..23}; do
    HOUR_STR=$(printf "%02d:00" $hour)
    # Aquí irían las queries para obtener datos por hora
    echo "<tr><td>$HOUR_STR</td><td>-</td><td>-</td><td>-</td></tr>" >> "$REPORT_FILE"
done

echo "</table>" >> "$REPORT_FILE"

# Incidentes del día
echo "<h2>Incidents and Alerts</h2>" >> "$REPORT_FILE"
INCIDENT_COUNT=$(grep "$(date '+%Y-%m-%d')" /var/log/incidents.log 2>/dev/null | wc -l)
echo "<div class='metric ok'><strong>Incidents Today:</strong> $INCIDENT_COUNT</div>" >> "$REPORT_FILE"

# Top queries lentas
echo "<h2>Top Slow Queries</h2>" >> "$REPORT_FILE"
echo "<table>" >> "$REPORT_FILE"
echo "<tr><th>Query</th><th>Calls</th><th>Mean Time (ms)</th><th>Total Time (ms)</th></tr>" >> "$REPORT_FILE"

psql -h localhost -p 5433 -U inmotech_readonly -d inmotech -tAc "
    SELECT 
        LEFT(query, 60) || '...' as query,
        calls,
        ROUND(mean_time, 2) as mean_time_ms,
        ROUND(total_time, 2) as total_time_ms
    FROM pg_stat_statements 
    ORDER BY mean_time DESC 
    LIMIT 10;
" 2>/dev/null | while IFS=$'\t' read -r query calls mean_time total_time; do
    echo "<tr><td>$query</td><td>$calls</td><td>$mean_time</td><td>$total_time</td></tr>" >> "$REPORT_FILE"
done

echo "</table>" >> "$REPORT_FILE"

# Cerrar HTML
echo "</body></html>" >> "$REPORT_FILE"

# Enviar reporte por email
mail -a "Content-Type: text/html" -s "InmoTech DB Daily Report - $REPORT_DATE" \
    ops@inmotech.com < "$REPORT_FILE"

echo "✅ Daily report generated and sent: $REPORT_FILE"
```

#### 📈 Reporte Semanal de Tendencias
```sql
-- Archivo: /reports/weekly_trends.sql

-- Tendencias de conexiones por día de la semana
SELECT 
    'Weekly Connection Trends' as report_section,
    EXTRACT(dow FROM timestamp) as day_of_week,
    CASE EXTRACT(dow FROM timestamp)
        WHEN 0 THEN 'Sunday'
        WHEN 1 THEN 'Monday'
        WHEN 2 THEN 'Tuesday'
        WHEN 3 THEN 'Wednesday'
        WHEN 4 THEN 'Thursday'
        WHEN 5 THEN 'Friday'
        WHEN 6 THEN 'Saturday'
    END as day_name,
    AVG(current_value) as avg_connections,
    MAX(current_value) as max_connections,
    MIN(current_value) as min_connections
FROM (
    SELECT 
        date_trunc('hour', created_at) as timestamp,
        COUNT(*) as current_value
    FROM audit_log
    WHERE created_at >= CURRENT_DATE - INTERVAL '7 days'
    GROUP BY date_trunc('hour', created_at)
) hourly_data
GROUP BY EXTRACT(dow FROM timestamp)
ORDER BY EXTRACT(dow FROM timestamp);

-- Tendencia de tamaño de BD
SELECT 
    'Database Growth Trend' as report_section,
    DATE(timestamp) as date,
    metric_value as size_mb,
    metric_value - LAG(metric_value) OVER (ORDER BY timestamp) as daily_growth_mb
FROM metrics_history 
WHERE metric_name = 'database_size_mb'
AND timestamp >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY timestamp;

-- Top usuarios por actividad
SELECT 
    'Top Users by Activity' as report_section,
    user_name,
    COUNT(*) as total_operations,
    COUNT(CASE WHEN operation = 'SELECT' THEN 1 END) as selects,
    COUNT(CASE WHEN operation = 'INSERT' THEN 1 END) as inserts,
    COUNT(CASE WHEN operation = 'UPDATE' THEN 1 END) as updates,
    COUNT(CASE WHEN operation = 'DELETE' THEN 1 END) as deletes
FROM audit_log 
WHERE timestamp >= CURRENT_DATE - INTERVAL '7 days'
GROUP BY user_name
ORDER BY total_operations DESC
LIMIT 10;
```

---

## 🎓 KNOWLEDGE TRANSFER Y TRAINING

### 1. Documentación Técnica Entregada

#### 📚 Manuales de Referencia
1. **Manual de Administración PostgreSQL InmoTech**
   - Configuración específica del servidor
   - Procedimientos de tuning y optimización
   - Troubleshooting común
   - Scripts de administración

2. **Manual de Backup y Recovery**
   - Procedimientos de backup automático y manual
   - Estrategias de recovery point-in-time
   - Disaster recovery procedures
   - Testing de backups

3. **Manual de Seguridad**
   - Configuración de usuarios y permisos
   - Auditoría y compliance
   - Incident response procedures
   - Security hardening checklist

4. **Manual de Monitoreo**
   - Configuración de alertas
   - Dashboard interpretation
   - Performance tuning basado en métricas
   - Capacity planning

#### 🛠️ Scripts y Herramientas
```bash
# Directorio de herramientas operacionales
/opt/inmotech/scripts/
├── monitoring/
│   ├── daily_health_check.sh
│   ├── performance_metrics.sh
│   ├── connection_monitor.sh
│   └── alert_manager.sh
├── backup/
│   ├── backup_inmotech.sh
│   ├── restore_procedure.sh
│   ├── verify_backup.sh
│   └── emergency_backup.sh
├── maintenance/
│   ├── vacuum_analyze.sh
│   ├── reindex_tables.sh
│   ├── update_statistics.sh
│   └── log_rotation.sh
├── troubleshooting/
│   ├── connection_troubleshoot.sh
│   ├── performance_analysis.sh
│   ├── disk_space_cleanup.sh
│   └── query_analysis.sh
└── reporting/
    ├── daily_report.sh
    ├── weekly_trends.sql
    ├── monthly_summary.sh
    └── sla_report.sh
```

### 2. Competencias Requeridas del Equipo de Soporte

#### 👤 Perfil: Database Operations Specialist

**Conocimientos Técnicos Mínimos:**
- [ ] **PostgreSQL Fundamentals:** Arquitectura, configuración, administración
- [ ] **SQL Avanzado:** Queries complejas, análisis de performance, tuning
- [ ] **Linux System Administration:** Ubuntu 22.04, comandos bash, crontab
- [ ] **Backup/Recovery:** Estrategias, herramientas, testing
- [ ] **Monitoring:** Interpretación de métricas, alertas, troubleshooting
- [ ] **Security:** Usuarios, permisos, auditoría, compliance
- [ ] **Scripting:** Bash scripts, automation, scheduling

**Certificaciones Deseables:**
- PostgreSQL Certified Professional
- Linux Professional Institute Certification (LPIC-1)
- Certified Information Security Manager (CISM)

#### 🎯 Training Plan (Duración: 2 semanas)

**Semana 1: Fundamentals y Environment Setup**

*Día 1-2: PostgreSQL InmoTech Overview*
- [ ] Arquitectura del sistema InmoTech
- [ ] Schema de base de datos detallado
- [ ] Configuración específica del servidor
- [ ] Usuarios y permisos establecidos

*Día 3-4: Operational Procedures*
- [ ] Daily health checks walkthrough
- [ ] Backup and restore procedures
- [ ] Monitoring dashboards y alertas
- [ ] Incident response procedures

*Día 5: Hands-on Practice*
- [ ] Ejecutar todos los scripts de monitoreo
- [ ] Simular y resolver incidentes comunes
- [ ] Práctica de backup y restore
- [ ] Review de logs y troubleshooting

**Semana 2: Advanced Operations y Certification**

*Día 6-7: Performance Tuning*
- [ ] Análisis de queries lentas
- [ ] Optimización de índices
- [ ] Tuning de configuración PostgreSQL
- [ ] Capacity planning

*Día 8-9: Security y Compliance*
- [ ] Procedimientos de auditoría
- [ ] Compliance GDPR/LOPD requirements
- [ ] Security incident response
- [ ] Access control y permissions management

*Día 10: Certification y Handover*
- [ ] **Examen práctico:** Resolución de scenarios reales
- [ ] **Certification:** Firma de competencias alcanzadas
- [ ] **Shadow operations:** 3 días de operación bajo supervisión

#### 📝 Checklist de Competencias

**Competencia Técnica Validada:**
- [ ] **Puede ejecutar daily health check** sin supervisión
- [ ] **Puede diagnosticar** problemas de conexión comunes
- [ ] **Puede realizar backup manual** en situación de emergencia
- [ ] **Puede interpretar** dashboards de monitoreo correctamente
- [ ] **Puede escalar incidentes** según procedimientos establecidos
- [ ] **Puede ejecutar restore** desde backup cifrado
- [ ] **Conoce ubicación** de toda la documentación crítica

**Competencia Operacional Validada:**
- [ ] **Understands SLAs** y response time requirements
- [ ] **Puede comunicarse** efectivamente durante incidentes
- [ ] **Conoce escalation matrix** y contactos clave
- [ ] **Puede generar reportes** operacionales básicos
- [ ] **Puede mantener** log books de operaciones
- [ ] **Sigue procedures** de seguridad y compliance

### 3. Recursos de Soporte Continuo

#### 📞 Canales de Soporte
1. **Internal Wiki:** `http://wiki.inmotech.com/database`
   - Documentación actualizada
   - Procedimientos step-by-step
   - FAQ común
   - Contact directory

2. **Slack Channels:**
   - `#inmotech-db-ops` - Operaciones diarias
   - `#inmotech-alerts` - Alertas automáticas
   - `#inmotech-incidents` - Coordinación de incidentes

3. **Knowledge Base:**
   - Base de datos de soluciones conocidas
   - Scripts validated
   - Lessons learned
   - Performance baselines

#### 🆘 Expert Support Available

**PostgreSQL Expert Support Contract:**
- **Provider:** PostgreSQL Professional Services
- **Coverage:** Business hours + emergency on-call
- **Response time:** 2 horas for critical issues
- **Contact:** +34 900-POSTGRESQL

**Monitoring Tools Support:**
- **Grafana/Prometheus:** Internal DevOps team
- **Custom dashboards:** Miguel Torres (miguel.torres@inmotech.com)

---

## 📋 CHECKLIST DE HANDOVER COMPLETO

### ✅ Entregables Técnicos Completados

#### Base de Datos y Configuración
- [ ] **PostgreSQL 14.x instalado** y configurado según especificaciones
- [ ] **Base de datos 'inmotech' creada** con todas las tablas implementadas
- [ ] **Usuarios configurados:** postgres, inmotech_admin, inmotech_app, inmotech_readonly
- [ ] **Permisos asignados** según principio de menor privilegio
- [ ] **Índices optimizados** para performance (23 índices implementados)
- [ ] **Sistema de auditoría** configurado y funcional
- [ ] **SSL/TLS habilitado** para todas las conexiones

#### Backup y Recovery
- [ ] **Backup automatizado** configurado (diario + incremental)
- [ ] **Cifrado AES-256** implementado en todos los backups
- [ ] **Retention policy** configurada (30 días local, 12 meses offsite)
- [ ] **Restore procedure** documentado y probado
- [ ] **Emergency backup script** disponible y funcional

#### Monitoreo y Alertas
- [ ] **Grafana dashboards** configurados y accesibles
- [ ] **Prometheus metrics** exportando datos correctamente
- [ ] **Alertas críticas** configuradas (CPU, memoria, conexiones, backups)
- [ ] **Custom metrics** implementados para InmoTech
- [ ] **Log aggregation** funcionando correctamente

#### Seguridad y Compliance
- [ ] **GDPR compliance** verificado y documentado
- [ ] **Penetration testing** completado sin issues críticos
- [ ] **Access controls** implementados y auditables
- [ ] **Encryption at rest** configurado donde requerido
- [ ] **Security monitoring** activo 24/7

### ✅ Documentación Entregada

#### Manuales Operacionales
- [ ] **Database Administration Manual** (45 páginas)
- [ ] **Backup/Recovery Procedures** (25 páginas)
- [ ] **Security and Compliance Guide** (35 páginas)
- [ ] **Monitoring and Alerting Guide** (20 páginas)
- [ ] **Troubleshooting Runbook** (30 páginas)

#### Scripts y Herramientas
- [ ] **Daily operations scripts** (health check, monitoring, reporting)
- [ ] **Backup and restore scripts** (manual y automated)
- [ ] **Troubleshooting tools** (connection analysis, performance)
- [ ] **Incident response scripts** (escalation, forensics)
- [ ] **Maintenance scripts** (vacuum, reindex, log rotation)

#### Configuración y Referencia
- [ ] **PostgreSQL configuration** documentada y versionada
- [ ] **Network and security** settings documentadas
- [ ] **User accounts and permissions** matrix completa
- [ ] **Monitoring thresholds** y alert definitions
- [ ] **SLA definitions** y measurement procedures

### ✅ Knowledge Transfer Completado

#### Team Training
- [ ] **Training plan ejecutado** (2 semanas intensive training)
- [ ] **Hands-on workshops** completados (todos los scripts y procedures)
- [ ] **Incident simulation** exercises realizados
- [ ] **Competency assessment** passed por todo el team
- [ ] **Shadow operations** completadas (3 días minimum)

#### Support Infrastructure
- [ ] **Expert support contracts** establecidos y probados
- [ ] **Escalation procedures** documentados y comunicados
- [ ] **Knowledge base** poblada con solutions y FAQ
- [ ] **Communication channels** configurados (Slack, email, phone)
- [ ] **Documentation access** configurado para equipo de soporte

### ✅ Operational Readiness

#### 24/7 Operations
- [ ] **On-call rotation** establecida y comunicada
- [ ] **Incident response procedures** probados end-to-end
- [ ] **Contact information** actualizada y distribuida
- [ ] **Emergency procedures** documentados y accesibles
- [ ] **Business continuity plan** implementado

#### Performance Baselines
- [ ] **Baseline metrics** establecidas y documentadas
- [ ] **SLA monitoring** configurado y functional
- [ ] **Capacity planning** data histórica disponible
- [ ] **Growth projections** documentadas para próximos 12 meses
- [ ] **Performance thresholds** calibrados según environment

---

## 📋 SIGN-OFF Y APROBACIONES

### Handover Técnico Completado
**Database Administrator (Development):** Carlos Martínez  
**Componentes transferidos:**
- [x] PostgreSQL configuration y database schema
- [x] Backup y recovery procedures
- [x] Performance tuning y optimization
- [x] Security configuration y hardening
- [x] Monitoring setup y custom metrics

**Knowledge transfer completado:** ✅  
**Documentation reviewed:** ✅  
**Operational procedures tested:** ✅

**Firma:** ________________  
**Fecha:** __/__/____

### Validación de Soporte Operacional
**Operations Manager:** [Nombre del Manager de Operaciones]  
**Team readiness confirmado:**
- [x] Staff trained y competency certified
- [x] 24/7 coverage established
- [x] Incident response procedures implemented
- [x] SLA monitoring configured
- [x] Expert support contracts activated

**Team ready para production support:** ✅  
**SLA compliance confirmed:** ✅  
**Escalation procedures tested:** ✅

**Firma:** ________________  
**Fecha:** __/__/____

### Aprobación de Seguridad y Compliance
**Security Officer:** Miguel Torres  
**Security requirements verified:**
- [x] GDPR/LOPD compliance implemented
- [x] Security controls tested
- [x] Access management configured
- [x] Audit trail functional
- [x] Incident response procedures validated

**Security clearance granted:** ✅  
**Compliance requirements met:** ✅  
**Risk assessment acceptable:** ✅

**Firma:** ________________  
**Fecha:** __/__/____

### Acceptance de Product Owner
**Technical Director/CTO:** [Nombre del Director Técnico]  
**Product requirements met:**
- [x] All Phase 1 components operational
- [x] Performance SLAs achievable
- [x] Security requirements satisfied
- [x] Operational procedures established
- [x] Team ready for ongoing support

**Phase 1 handover accepted:** ✅  
**Authorization para proceed to Phase 2:** ✅  
**Development team released from Phase 1:** ✅

**Firma:** ________________  
**Fecha:** __/__/____

---

## 📞 CONTACTOS DE EMERGENCIA POST-HANDOVER

### Matriz de Contactos de Escalación

**Nivel 1: Equipo de Operaciones (24/7)**
- **Contacto Principal:** Operador de Turno
- **Teléfono:** +34 900-123-456
- **Email:** ops@inmotech.com
- **Slack:** #inmotech-ops
- **Tiempo de respuesta:** 15 minutos

**Nivel 2: Experto en Base de Datos (Guardia)**
- **Principal:** Carlos Martínez (30 días post-traspaso)
- **Teléfono:** +34 600-789-123
- **Email:** carlos.martinez@inmotech.com
- **Respaldo:** PostgreSQL Professional Services
- **Tiempo de respuesta:** 1 hora

**Nivel 3: Liderazgo Técnico**
- **Líder DevOps:** Miguel Torres
- **Teléfono:** +34 600-789-125
- **Email:** miguel.torres@inmotech.com
- **Tiempo de respuesta:** 2 horas

**Nivel 4: Escalación Ejecutiva**
- **CTO:** [Nombre del CTO]
- **Teléfono:** +34 600-000-999
- **Email:** cto@inmotech.com

### Recursos de Soporte

**Soporte Externo:**
- **PostgreSQL Professional Services:** +34 900-POSTGRESQL
- **Soporte AWS:** Tier empresarial (infraestructura de base de datos)
- **Respuesta a Incidentes de Seguridad:** security@inmotech.com

**Documentation and Knowledge Base:**
- **Internal Wiki:** http://wiki.inmotech.com/database
- **Grafana Dashboards:** http://monitoring.inmotech.com:3000
- **Incident Tracking:** http://tickets.inmotech.com

---

*Documento de Handover y Soporte para el Proyecto InmoTech - Sistema de Gestión Inmobiliaria*  
*Fase 1: Base de Datos y Migraciones | Enero 2026 | Transición a Operaciones*