# Checklist de Cumplimiento de Seguridad - Fase 1: Base de Datos y Migraciones

## 📋 Información del Proyecto
- **Proyecto:** InmoTech - Sistema de Gestión Inmobiliaria
- **Fase:** Fase 1 - Base de Datos y Migraciones
- **Período de Evaluación:** 06/01/2026 - 10/01/2026
- **Security Lead:** Carlos Martínez - Database Administrator
- **Security Reviewer:** Miguel Torres - DevOps/Security
- **Versión:** 1.0

---

## 🎯 Objetivo del Checklist

### Objetivo Principal
Asegurar que la implementación de la Fase 1 (Base de Datos y Migraciones) del sistema InmoTech cumple con todos los estándares de seguridad corporativos, regulaciones de protección de datos y mejores prácticas de seguridad en PostgreSQL.

### Marcos de Cumplimiento
- ✅ **GDPR** (Reglamento General de Protección de Datos - UE)
- ✅ **LOPD-GDD** (Ley Orgánica de Protección de Datos - España)
- ✅ **ISO 27001:2013** (Sistema de Gestión de Seguridad de la Información)
- ✅ **OWASP Database Security** (Top 10 Database Security Risks)
- ✅ **PCI-DSS** (Payment Card Industry Data Security Standard) - Nivel básico

---

## 🔒 1. SEGURIDAD DE AUTENTICACIÓN Y AUTORIZACIÓN

### 1.1 Configuración de Usuarios y Roles PostgreSQL

#### ✅ Usuarios de Base de Datos
- [ ] **Usuario Administrador DBA:** Separado del usuario aplicación
  ```sql
  -- Verificar usuarios existentes
  SELECT usename, usesuper, usecreatedb, userepl, valuntil 
  FROM pg_user 
  ORDER BY usename;
  ```
  - [ ] `postgres`: Solo para mantenimiento crítico (sin acceso aplicación)
  - [ ] `inmotech_admin`: Administración BD (DDL permitido)
  - [ ] `inmotech_app`: Usuario aplicación (solo DML)
  - [ ] `inmotech_readonly`: Solo lectura (reportes/análisis)

#### ✅ Política de Contraseñas
- [ ] **Longitud mínima:** 16 caracteres
- [ ] **Complejidad:** Mayúsculas + minúsculas + números + símbolos
- [ ] **Rotación:** Cambio cada 90 días para usuarios privilegiados
- [ ] **No reutilización:** Últimas 5 contraseñas no reutilizables
- [ ] **Encriptación:** Contraseñas almacenadas con hash scram-sha-256

**Verificación:**
```sql
-- Verificar método de autenticación
SHOW password_encryption;
-- Resultado esperado: scram-sha-256

-- Verificar configuración en pg_hba.conf
SELECT type, database, user_name, auth_method 
FROM pg_hba_file_rules 
WHERE database = 'inmotech';
```

#### ✅ Privilegios Mínimos (Principle of Least Privilege)
- [ ] **Usuario aplicación (`inmotech_app`):**
  - [ ] `SELECT, INSERT, UPDATE, DELETE` solo en tablas específicas
  - [ ] Sin permisos de `DROP`, `CREATE`, `ALTER`
  - [ ] Sin acceso a tablas del sistema PostgreSQL

- [ ] **Usuario readonly (`inmotech_readonly`):**
  - [ ] Solo permisos `SELECT` en vistas específicas
  - [ ] Sin acceso a datos sensibles (passwords, tokens)

**Script de Verificación:**
```sql
-- Verificar privilegios por usuario
SELECT 
    grantee, 
    table_name, 
    privilege_type 
FROM information_schema.role_table_grants 
WHERE table_schema = 'public' 
ORDER BY grantee, table_name;

-- Verificar que usuario app no tenga permisos DDL
SELECT 
    grantee,
    object_name,
    privilege_type
FROM information_schema.usage_privileges 
WHERE grantee = 'inmotech_app';
```

### 1.2 Seguridad de Sesiones

#### ✅ Configuración de Conexiones
- [ ] **SSL/TLS obligatorio:** Todas las conexiones cifradas
- [ ] **Timeout de sesión:** 30 minutos de inactividad
- [ ] **Límite de conexiones concurrentes:** Por usuario definido
- [ ] **Logging de conexiones:** Todas las conexiones/desconexiones registradas

**Configuración requerida en `postgresql.conf`:**
```ini
# SSL Configuration
ssl = on
ssl_cert_file = '/etc/ssl/certs/postgresql.crt'
ssl_key_file = '/etc/ssl/private/postgresql.key'
ssl_ca_file = '/etc/ssl/certs/ca.crt'
ssl_ciphers = 'HIGH:MEDIUM:+3DES:!aNULL'

# Connection Limits
max_connections = 200
superuser_reserved_connections = 5

# Timeouts
statement_timeout = 300000  # 5 minutes
idle_in_transaction_session_timeout = 1800000  # 30 minutes
```

**Verificación SSL:**
```sql
-- Verificar que SSL está habilitado
SELECT name, setting FROM pg_settings WHERE name = 'ssl';

-- Verificar conexiones SSL activas
SELECT application_name, client_addr, ssl, ssl_version, ssl_cipher
FROM pg_stat_ssl 
JOIN pg_stat_activity USING (pid);
```

---

## 🛡️ 2. PROTECCIÓN DE DATOS SENSIBLES

### 2.1 Cifrado de Datos en Reposo

#### ✅ Cifrado de Base de Datos
- [ ] **Cifrado TDE:** Transparent Data Encryption habilitado en PostgreSQL
- [ ] **Cifrado de Backup:** Todos los backups cifrados con AES-256
- [ ] **Cifrado de WAL:** Write-Ahead Logs cifrados
- [ ] **Cifrado de tablespaces:** Espacios de tabla críticos cifrados

**Verificación:**
```bash
# Verificar cifrado de archivos de datos
ls -la /var/lib/postgresql/14/main/
file /var/lib/postgresql/14/main/base/*/[0-9]*

# Verificar cifrado en pg_settings
psql -c "SELECT name, setting FROM pg_settings WHERE name LIKE '%encrypt%';"
```

#### ✅ Cifrado de Datos Sensibles a Nivel de Aplicación
- [ ] **Contraseñas usuario:** Hash con bcrypt (cost factor ≥ 12)
- [ ] **Tokens de sesión:** Cifrados con AES-256-GCM
- [ ] **Datos PII:** Datos personales identificables cifrados
- [ ] **Información financiera:** Precios/comisiones con protección adicional

**Script de Validación:**
```sql
-- Verificar que no hay contraseñas en texto plano
SELECT 'Password validation' as test,
       CASE 
           WHEN EXISTS (
               SELECT 1 FROM users 
               WHERE password_hash NOT LIKE '$2b$%' 
               OR length(password_hash) < 60
           )
           THEN '❌ FAIL: Plain text passwords found'
           ELSE '✅ PASS: All passwords properly hashed'
       END as result;

-- Verificar longitud de campos sensibles
SELECT 
    'Field encryption check' as test,
    COUNT(*) as total_users,
    AVG(length(password_hash)) as avg_hash_length,
    MIN(length(password_hash)) as min_hash_length
FROM users;
```

### 2.2 Enmascaramiento y Anonimización

#### ✅ Datos de Prueba y Desarrollo
- [ ] **Email masking:** Emails reales reemplazados en entornos no productivos
- [ ] **Phone masking:** Números de teléfono ficticios
- [ ] **Address masking:** Direcciones genéricas para testing
- [ ] **PII anonymization:** Datos personales irreconocibles en dev/test

**Script de Anonimización para Desarrollo:**
```sql
-- Script para anonimizar datos de desarrollo
-- Archivo: scripts/anonymize_dev_data.sql

BEGIN;

-- Anonimizar emails (conservar formato para testing)
UPDATE users 
SET email = 'user' || id || '@inmotech-dev.local'
WHERE email NOT LIKE '%@inmotech-dev.local';

-- Anonimizar números de teléfono
UPDATE users 
SET phone = '+34' || LPAD((600000000 + id)::text, 9, '0')
WHERE phone IS NOT NULL;

-- Anonimizar nombres (conservar iniciales)
UPDATE users 
SET first_name = LEFT(first_name, 1) || REPEAT('*', LENGTH(first_name) - 1),
    last_name = LEFT(last_name, 1) || REPEAT('*', LENGTH(last_name) - 1);

-- Anonimizar direcciones de propiedades
UPDATE properties 
SET location = 'Calle Ficticia ' || id || ', Madrid, España'
WHERE location NOT LIKE 'Calle Ficticia%';

COMMIT;
```

---

## 🔍 3. AUDITORÍA Y LOGGING

### 3.1 Configuración de Auditoría PostgreSQL

#### ✅ Logging Obligatorio
- [ ] **Todas las conexiones:** Inicio y fin de sesión
- [ ] **Comandos DDL:** CREATE, ALTER, DROP operations
- [ ] **Acceso a datos sensibles:** SELECT en tablas de usuarios
- [ ] **Modificaciones críticas:** INSERT, UPDATE, DELETE en tablas principales
- [ ] **Fallos de autenticación:** Intentos de login fallidos
- [ ] **Escalación de privilegios:** Uso de comandos administrativos

**Configuración requerida en `postgresql.conf`:**
```ini
# Logging Configuration
logging_collector = on
log_directory = '/var/log/postgresql'
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
log_file_mode = 0600
log_rotation_age = 1d
log_rotation_size = 100MB

# Audit Logging
log_connections = on
log_disconnections = on
log_duration = on
log_line_prefix = '%t [%p]: [%l-1] user=%u,db=%d,app=%a,client=%h '
log_lock_waits = on
log_statement = 'ddl'
log_min_duration_statement = 1000  # Log queries > 1 second

# Security Events
log_checkpoints = on
log_autovacuum_min_duration = 0
```

#### ✅ Audit Trail Personalizado
- [ ] **Tabla de auditoría:** Registro de cambios críticos
- [ ] **Triggers de auditoría:** Automáticos en tablas sensibles
- [ ] **Retención de logs:** Mínimo 12 meses
- [ ] **Integridad de logs:** Protección contra modificación

**Implementación de Auditoría:**
```sql
-- Crear tabla de auditoría
CREATE TABLE audit_log (
    id SERIAL PRIMARY KEY,
    table_name VARCHAR(50) NOT NULL,
    operation VARCHAR(10) NOT NULL,  -- INSERT, UPDATE, DELETE
    user_name VARCHAR(100) NOT NULL,
    timestamp TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    old_values JSONB,
    new_values JSONB,
    client_ip INET,
    application_name VARCHAR(100)
);

-- Crear índices para consultas de auditoría
CREATE INDEX idx_audit_table_timestamp ON audit_log(table_name, timestamp);
CREATE INDEX idx_audit_user_timestamp ON audit_log(user_name, timestamp);
CREATE INDEX idx_audit_operation ON audit_log(operation);

-- Función de trigger para auditoría
CREATE OR REPLACE FUNCTION audit_trigger_function()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'DELETE' THEN
        INSERT INTO audit_log (
            table_name, operation, user_name, old_values, client_ip, application_name
        ) VALUES (
            TG_TABLE_NAME, TG_OP, current_user, 
            row_to_json(OLD), inet_client_addr(), current_setting('application_name', true)
        );
        RETURN OLD;
    ELSIF TG_OP = 'UPDATE' THEN
        INSERT INTO audit_log (
            table_name, operation, user_name, old_values, new_values, client_ip, application_name
        ) VALUES (
            TG_TABLE_NAME, TG_OP, current_user, 
            row_to_json(OLD), row_to_json(NEW), inet_client_addr(), current_setting('application_name', true)
        );
        RETURN NEW;
    ELSIF TG_OP = 'INSERT' THEN
        INSERT INTO audit_log (
            table_name, operation, user_name, new_values, client_ip, application_name
        ) VALUES (
            TG_TABLE_NAME, TG_OP, current_user, 
            row_to_json(NEW), inet_client_addr(), current_setting('application_name', true)
        );
        RETURN NEW;
    END IF;
    RETURN NULL;
END;
$$ LANGUAGE plpgsql;

-- Aplicar triggers a tablas críticas
CREATE TRIGGER audit_users_trigger
    AFTER INSERT OR UPDATE OR DELETE ON users
    FOR EACH ROW EXECUTE FUNCTION audit_trigger_function();

CREATE TRIGGER audit_properties_trigger
    AFTER INSERT OR UPDATE OR DELETE ON properties
    FOR EACH ROW EXECUTE FUNCTION audit_trigger_function();

CREATE TRIGGER audit_transactions_trigger
    AFTER INSERT OR UPDATE OR DELETE ON transactions
    FOR EACH ROW EXECUTE FUNCTION audit_trigger_function();
```

### 3.2 Monitoreo de Seguridad

#### ✅ Alertas Automáticas
- [ ] **Intentos de acceso no autorizados:** > 5 fallos login en 5 min
- [ ] **Actividad fuera de horario:** Conexiones 22:00-06:00
- [ ] **Queries sospechosas:** Acceso masivo a datos sensibles
- [ ] **Cambios en esquema:** DDL no programados
- [ ] **Escalación de privilegios:** Uso de comandos administrativos

**Script de Monitoreo de Seguridad:**
```bash
#!/bin/bash
# Archivo: scripts/security_monitoring.sh

LOG_FILE="/var/log/postgresql/postgresql-$(date +%Y-%m-%d)*.log"
ALERT_EMAIL="security@inmotech.com"
ALERT_LOG="/var/log/security_alerts.log"

# Function to send alert
send_alert() {
    local subject="$1"
    local message="$2"
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $subject: $message" >> "$ALERT_LOG"
    echo "$message" | mail -s "$subject - InmoTech Security Alert" "$ALERT_EMAIL"
}

# Check for failed authentication attempts
FAILED_ATTEMPTS=$(grep -c "FATAL.*authentication failed" $LOG_FILE 2>/dev/null || echo 0)
if [ "$FAILED_ATTEMPTS" -gt 10 ]; then
    send_alert "High Failed Login Attempts" "Detected $FAILED_ATTEMPTS failed authentication attempts in the last hour"
fi

# Check for off-hours database access
CURRENT_HOUR=$(date +%H)
if [ "$CURRENT_HOUR" -lt 6 ] || [ "$CURRENT_HOUR" -gt 22 ]; then
    OFF_HOURS_CONNECTIONS=$(grep -c "connection authorized" $LOG_FILE 2>/dev/null || echo 0)
    if [ "$OFF_HOURS_CONNECTIONS" -gt 0 ]; then
        send_alert "Off-Hours Database Access" "Detected $OFF_HOURS_CONNECTIONS connections outside business hours"
    fi
fi

# Check for suspicious queries (SELECT * FROM sensitive tables)
SUSPICIOUS_QUERIES=$(grep -c "SELECT.*FROM.*users.*\*" $LOG_FILE 2>/dev/null || echo 0)
if [ "$SUSPICIOUS_QUERIES" -gt 0 ]; then
    send_alert "Suspicious Data Access" "Detected potential data mining queries on sensitive tables"
fi

# Check for DDL operations outside maintenance windows
DDL_OPERATIONS=$(grep -c -E "(CREATE|ALTER|DROP).*TABLE" $LOG_FILE 2>/dev/null || echo 0)
if [ "$DDL_OPERATIONS" -gt 0 ]; then
    send_alert "Unscheduled DDL Operations" "Detected $DDL_OPERATIONS schema changes outside maintenance window"
fi
```

---

## 🔐 4. CONTROL DE ACCESO Y PERMISOS

### 4.1 Segregación de Funciones

#### ✅ Roles Funcionales Definidos
- [ ] **Database Administrator (DBA):** Carlos Martínez
  - [ ] Acceso completo PostgreSQL
  - [ ] Gestión de usuarios y permisos
  - [ ] Backup y recovery
  - [ ] Performance tuning

- [ ] **Application Developer:** Ana García
  - [ ] Acceso desarrollo con usuario limitado
  - [ ] Sin acceso productivo directo
  - [ ] Deploy a través de CI/CD únicamente

- [ ] **DevOps Engineer:** Miguel Torres
  - [ ] Acceso infraestructura y monitoring
  - [ ] Gestión de backups
  - [ ] Sin acceso directo a datos de aplicación

- [ ] **QA Tester:** Laura Pérez
  - [ ] Acceso solo a entorno de testing
  - [ ] Datos anonimizados únicamente
  - [ ] Sin acceso a entorno productivo

**Verificación de Segregación:**
```sql
-- Verificar que cada usuario tiene solo los permisos necesarios
SELECT 
    r.rolname as role_name,
    r.rolcanlogin as can_login,
    r.rolcreatedb as can_create_db,
    r.rolcreaterole as can_create_role,
    r.rolsuper as is_superuser,
    ARRAY(
        SELECT b.rolname
        FROM pg_catalog.pg_auth_members m
        JOIN pg_catalog.pg_roles b ON (m.roleid = b.oid)
        WHERE m.member = r.oid
    ) as member_of
FROM pg_catalog.pg_roles r
WHERE r.rolname NOT LIKE 'pg_%'
ORDER BY r.rolname;
```

### 4.2 Acceso por Rangos IP

#### ✅ Restricción de Acceso por Red
- [ ] **Red interna corporativa:** 192.168.1.0/24 (acceso completo)
- [ ] **VPN desarrolladores:** 10.0.1.0/24 (acceso limitado)
- [ ] **Servidores aplicación:** IPs específicas (acceso aplicación)
- [ ] **Bloqueo Internet público:** Sin acceso directo desde Internet

**Configuración `pg_hba.conf` requerida:**
```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# Local connections
local   all             postgres                                peer
local   inmotech        inmotech_admin                          scram-sha-256

# Internal corporate network
hostssl inmotech        inmotech_admin  192.168.1.0/24         scram-sha-256
hostssl inmotech        inmotech_app    192.168.1.0/24         scram-sha-256

# VPN for developers (read-only)
hostssl inmotech        inmotech_readonly 10.0.1.0/24         scram-sha-256

# Application servers (specific IPs)
hostssl inmotech        inmotech_app    192.168.1.10/32        scram-sha-256
hostssl inmotech        inmotech_app    192.168.1.11/32        scram-sha-256

# Deny all other connections
host    all             all             0.0.0.0/0               reject
```

---

## 🛠️ 5. CONFIGURACIÓN SEGURA DEL SISTEMA

### 5.1 Hardening PostgreSQL

#### ✅ Configuraciones de Seguridad
- [ ] **Versión PostgreSQL:** 14.x (últimos patches aplicados)
- [ ] **Puerto no estándar:** Puerto 5433 (no 5432 por defecto)
- [ ] **Bind address:** Solo IPs internas (no 0.0.0.0)
- [ ] **Shared_preload_libraries:** Solo módulos necesarios
- [ ] **Parámetros de memoria:** Configurados según capacity planning

**Verificación de Configuración:**
```sql
-- Verificar versión y configuraciones críticas
SELECT name, setting, unit, category 
FROM pg_settings 
WHERE name IN (
    'version',
    'port',
    'listen_addresses',
    'shared_preload_libraries',
    'max_connections',
    'shared_buffers',
    'work_mem',
    'maintenance_work_mem',
    'ssl',
    'password_encryption'
)
ORDER BY name;

-- Verificar extensiones instaladas
SELECT extname, extversion 
FROM pg_extension;
```

#### ✅ Permisos del Sistema Operativo
- [ ] **Usuario PostgreSQL:** Cuenta dedicada sin shell interactivo
- [ ] **Directorio de datos:** Permisos 700 (solo postgres user)
- [ ] **Archivos de configuración:** Permisos 600
- [ ] **Logs:** Permisos 640 (postgres:adm groups)
- [ ] **Backups:** Cifrados y permisos 600

**Script de Verificación SO:**
```bash
#!/bin/bash
# Verificación de permisos del sistema

echo "=== PostgreSQL Security Hardening Verification ==="

# Verificar usuario postgresql
echo "PostgreSQL User Configuration:"
id postgres
grep postgres /etc/passwd | cut -d: -f7  # Shell should be /bin/false

# Verificar permisos de directorio de datos
echo -e "\nData Directory Permissions:"
ls -ld /var/lib/postgresql/
ls -ld /var/lib/postgresql/14/
ls -ld /var/lib/postgresql/14/main/

# Verificar permisos de archivos de configuración
echo -e "\nConfiguration Files Permissions:"
ls -l /etc/postgresql/14/main/postgresql.conf
ls -l /etc/postgresql/14/main/pg_hba.conf
ls -l /etc/postgresql/14/main/pg_ident.conf

# Verificar permisos de logs
echo -e "\nLog Files Permissions:"
ls -l /var/log/postgresql/

# Verificar que PostgreSQL no corre como root
echo -e "\nPostgreSQL Process User:"
ps aux | grep postgres | head -5
```

### 5.2 Backup y Recovery Seguros

#### ✅ Estrategia de Backup Segura
- [ ] **Backups cifrados:** AES-256 encryption obligatorio
- [ ] **Almacenamiento offsite:** Copias en ubicación separada
- [ ] **Retention policy:** 30 días backup diarios, 12 meses mensuales
- [ ] **Verificación de integridad:** Testing de restore mensual
- [ ] **Control de acceso:** Solo personal autorizado accede a backups

**Script de Backup Seguro:**
```bash
#!/bin/bash
# Archivo: scripts/secure_backup.sh

BACKUP_DIR="/backups/postgresql"
PGPASSWORD="$POSTGRES_PASSWORD"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="inmotech_backup_$DATE.sql"
ENCRYPTED_FILE="$BACKUP_FILE.gpg"

# Crear backup
echo "Creating backup at $(date)"
pg_dump -h localhost -U postgres -d inmotech \
    --clean --create --verbose \
    --file="$BACKUP_DIR/$BACKUP_FILE"

if [ $? -eq 0 ]; then
    echo "✅ Backup created successfully"
    
    # Cifrar backup
    gpg --symmetric --cipher-algo AES256 \
        --compress-algo 1 --compress-level 9 \
        --output "$BACKUP_DIR/$ENCRYPTED_FILE" \
        "$BACKUP_DIR/$BACKUP_FILE"
    
    if [ $? -eq 0 ]; then
        echo "✅ Backup encrypted successfully"
        
        # Eliminar backup sin cifrar
        rm "$BACKUP_DIR/$BACKUP_FILE"
        
        # Verificar integridad
        gpg --quiet --decrypt "$BACKUP_DIR/$ENCRYPTED_FILE" | head -10 > /dev/null
        if [ $? -eq 0 ]; then
            echo "✅ Backup integrity verified"
            
            # Aplicar permisos seguros
            chmod 600 "$BACKUP_DIR/$ENCRYPTED_FILE"
            chown postgres:postgres "$BACKUP_DIR/$ENCRYPTED_FILE"
            
        else
            echo "❌ Backup integrity check failed"
            exit 1
        fi
    else
        echo "❌ Backup encryption failed"
        exit 1
    fi
else
    echo "❌ Backup creation failed"
    exit 1
fi

# Cleanup old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.gpg" -type f -mtime +30 -delete

echo "Backup process completed at $(date)"
```

---

## 🚨 6. RESPUESTA A INCIDENTES DE SEGURIDAD

### 6.1 Procedimientos de Respuesta

#### ✅ Escalation Matrix
1. **Level 1 - Automático (0-5 min):**
   - [ ] Sistema de monitoreo detecta anomalía
   - [ ] Alerta automática enviada al equipo
   - [ ] Logging detallado activado
   - [ ] Procedimientos automáticos de contención

2. **Level 2 - DBA Response (5-30 min):**
   - [ ] Carlos Martínez (DBA) recibe alerta
   - [ ] Análisis inicial de logs
   - [ ] Implementación de medidas de contención
   - [ ] Escalación si es necesario

3. **Level 3 - Security Team (30+ min):**
   - [ ] Escalación a Miguel Torres (DevOps/Security)
   - [ ] Investigación forense
   - [ ] Coordinación con stakeholders
   - [ ] Plan de remediación

#### ✅ Playbook de Incidentes Comunes

**Incidente: Acceso No Autorizado Detectado**
```bash
#!/bin/bash
# Archivo: security/incident_unauthorized_access.sh

echo "=== INCIDENT RESPONSE: Unauthorized Access ==="
echo "Incident started at: $(date)"

# 1. Immediate containment
echo "Step 1: Immediate containment"
psql -d inmotech -c "
    SELECT pg_terminate_backend(pid) 
    FROM pg_stat_activity 
    WHERE usename NOT IN ('postgres', 'inmotech_app', 'inmotech_admin')
    AND state = 'active';
"

# 2. Disable potentially compromised accounts
echo "Step 2: Disable suspicious accounts"
psql -d inmotech -c "
    UPDATE users 
    SET account_locked = true, 
        locked_at = CURRENT_TIMESTAMP,
        locked_reason = 'Security incident - unauthorized access detected'
    WHERE last_login > CURRENT_TIMESTAMP - INTERVAL '1 hour'
    AND suspicious_activity_flag = true;
"

# 3. Enhanced logging
echo "Step 3: Enable enhanced logging"
psql -c "ALTER SYSTEM SET log_statement = 'all';"
psql -c "SELECT pg_reload_conf();"

# 4. Capture current state for forensics
echo "Step 4: Forensic data capture"
FORENSIC_DIR="/var/log/security/incident_$(date +%Y%m%d_%H%M%S)"
mkdir -p "$FORENSIC_DIR"

psql -d inmotech -c "COPY (
    SELECT * FROM pg_stat_activity 
) TO '$FORENSIC_DIR/active_sessions.csv' CSV HEADER;"

psql -d inmotech -c "COPY (
    SELECT * FROM audit_log 
    WHERE timestamp > CURRENT_TIMESTAMP - INTERVAL '2 hours'
) TO '$FORENSIC_DIR/recent_audit.csv' CSV HEADER;"

# 5. Alert security team
echo "Step 5: Alert security team"
ALERT_MSG="SECURITY INCIDENT: Unauthorized access detected in InmoTech database at $(date). Immediate containment measures applied. Forensic data captured in $FORENSIC_DIR. Please investigate immediately."

echo "$ALERT_MSG" | mail -s "URGENT: Security Incident - InmoTech DB" security@inmotech.com

echo "Incident response completed at: $(date)"
echo "Forensic data location: $FORENSIC_DIR"
```

**Incidente: Sospecha de Inyección SQL**
```bash
#!/bin/bash
# Archivo: security/incident_sql_injection.sh

echo "=== INCIDENT RESPONSE: Suspected SQL Injection ==="

# 1. Identify suspicious queries
echo "Step 1: Identifying suspicious queries"
psql -d inmotech -c "
    SELECT query_start, usename, application_name, client_addr, query
    FROM pg_stat_activity 
    WHERE query ILIKE '%union%select%' 
       OR query ILIKE '%drop%table%'
       OR query ILIKE '%''%or%''%'
       OR query ILIKE '%admin%''%--'
       OR query ILIKE '%1=1%'
    ORDER BY query_start DESC;
"

# 2. Block suspicious IP addresses temporarily
echo "Step 2: Implementing IP blocks"
SUSPICIOUS_IPS=$(psql -d inmotech -tAc "
    SELECT DISTINCT client_addr 
    FROM pg_stat_activity 
    WHERE query ILIKE '%union%select%' 
       OR query ILIKE '%drop%table%'
")

for ip in $SUSPICIOUS_IPS; do
    if [ "$ip" != "" ]; then
        echo "Blocking IP: $ip"
        iptables -A INPUT -s "$ip" -j DROP
    fi
done

# 3. Enhanced monitoring
echo "Step 3: Enhanced monitoring enabled"
psql -c "ALTER SYSTEM SET log_min_duration_statement = 0;"
psql -c "ALTER SYSTEM SET log_statement = 'all';"
psql -c "SELECT pg_reload_conf();"

echo "SQL injection response completed"
```

### 6.2 Forensics y Análisis Post-Incidente

#### ✅ Retención y Análisis de Evidencias
- [ ] **Logs preservados:** Mínimo 12 meses sin modificación
- [ ] **Snapshots de sistema:** Estado capturado durante incidente
- [ ] **Análisis forense:** Herramientas especializadas disponibles
- [ ] **Documentación:** Cronología completa del incidente
- [ ] **Lecciones aprendidas:** Mejoras implementadas post-incidente

**Script de Análisis Forense:**
```sql
-- Archivo: security/forensic_analysis.sql

-- Análisis de actividad sospechosa en últimas 24h
WITH suspicious_activity AS (
    SELECT 
        timestamp,
        user_name,
        table_name,
        operation,
        client_ip,
        application_name,
        old_values,
        new_values
    FROM audit_log 
    WHERE timestamp > CURRENT_TIMESTAMP - INTERVAL '24 hours'
    AND (
        -- Actividad fuera de horario laboral
        EXTRACT(hour FROM timestamp) NOT BETWEEN 8 AND 18
        -- O múltiples operaciones de borrado
        OR (operation = 'DELETE' AND table_name IN ('users', 'properties', 'transactions'))
        -- O acceso desde IPs no corporativas
        OR client_ip NOT IN (SELECT ip FROM authorized_ips)
    )
)
SELECT * FROM suspicious_activity
ORDER BY timestamp DESC;

-- Análisis de escalación de privilegios
SELECT 
    usename,
    COUNT(*) as suspicious_operations,
    array_agg(DISTINCT query) as unique_queries
FROM pg_stat_statements s
JOIN pg_stat_activity a ON s.userid = a.usesysid
WHERE query ILIKE ANY(ARRAY[
    '%grant%', '%create user%', '%alter user%', '%drop user%',
    '%create role%', '%alter role%', '%drop role%'
])
AND s.last_exec > CURRENT_TIMESTAMP - INTERVAL '24 hours'
GROUP BY usename
HAVING COUNT(*) > 5;  -- Más de 5 operaciones privilegiadas es sospechoso

-- Análisis de acceso a datos sensibles
SELECT 
    user_name,
    COUNT(*) as access_count,
    COUNT(DISTINCT table_name) as tables_accessed,
    string_agg(DISTINCT table_name, ', ') as sensitive_tables
FROM audit_log 
WHERE table_name IN ('users', 'properties', 'transactions', 'files')
AND timestamp > CURRENT_TIMESTAMP - INTERVAL '24 hours'
GROUP BY user_name
HAVING COUNT(*) > 100  -- Más de 100 accesos es sospechoso
ORDER BY access_count DESC;
```

---

## ✅ 7. CHECKLIST DE VALIDACIÓN FINAL

### 7.1 Validación Técnica

#### ✅ Configuración PostgreSQL
- [ ] **PostgreSQL 14.x instalado** con últimos security patches
- [ ] **SSL/TLS habilitado** y configurado correctamente
- [ ] **Cifrado de contraseñas** configurado (scram-sha-256)
- [ ] **Logging de auditoría** habilitado y funcional
- [ ] **Permisos de usuarios** configurados según principio de menor privilegio
- [ ] **Backup automatizado** cifrado y probado
- [ ] **Monitoreo de seguridad** activo y alertas configuradas

#### ✅ Verificación de Cumplimiento GDPR/LOPD
- [ ] **Consentimiento de datos** - Mecanismo implementado
- [ ] **Derecho de acceso** - Consulta de datos personales disponible
- [ ] **Derecho de rectificación** - Actualización de datos implementada
- [ ] **Derecho de supresión** - Borrado de datos implementado
- [ ] **Portabilidad de datos** - Exportación en formato estándar
- [ ] **Notificación de brechas** - Procedimiento definido (72h)

### 7.2 Testing de Penetración

#### ✅ Security Tests Ejecutados
- [ ] **Brute force testing** - Intentos de login masivos bloqueados
- [ ] **SQL injection testing** - Queries maliciosas bloqueadas
- [ ] **Privilege escalation** - Escalación de permisos prevenida
- [ ] **Network scanning** - Puertos no autorizados cerrados
- [ ] **SSL/TLS testing** - Certificados válidos y cifrados seguros
- [ ] **Backup security** - Acceso no autorizado a backups prevenido

#### ✅ Vulnerability Assessment
```bash
#!/bin/bash
# Archivo: security/vulnerability_scan.sh

echo "=== VULNERABILITY ASSESSMENT ==="

# 1. PostgreSQL version check
echo "PostgreSQL Version Check:"
psql -c "SELECT version();" | grep -E "(14\.[0-9]+)"
if [ $? -eq 0 ]; then
    echo "✅ PostgreSQL version is supported"
else
    echo "❌ PostgreSQL version may have vulnerabilities"
fi

# 2. Check for default passwords
echo -e "\nDefault Password Check:"
if psql -U postgres -c "SELECT 1;" 2>/dev/null; then
    echo "❌ WARNING: Default postgres user can connect without password"
else
    echo "✅ Default postgres user requires authentication"
fi

# 3. Check SSL configuration
echo -e "\nSSL Configuration Check:"
SSL_STATUS=$(psql -tAc "SELECT setting FROM pg_settings WHERE name='ssl';")
if [ "$SSL_STATUS" = "on" ]; then
    echo "✅ SSL is enabled"
else
    echo "❌ SSL is disabled - security vulnerability"
fi

# 4. Check for weak cipher suites
echo -e "\nSSL Cipher Check:"
openssl s_client -connect localhost:5433 -cipher 'LOW:EXPORT' < /dev/null 2>/dev/null
if [ $? -eq 0 ]; then
    echo "❌ Weak ciphers are allowed"
else
    echo "✅ Weak ciphers are disabled"
fi

# 5. File permission checks
echo -e "\nFile Permission Check:"
POSTGRES_CONFIG="/etc/postgresql/14/main/postgresql.conf"
CONFIG_PERMS=$(stat -c "%a" "$POSTGRES_CONFIG" 2>/dev/null)
if [ "$CONFIG_PERMS" = "640" ] || [ "$CONFIG_PERMS" = "600" ]; then
    echo "✅ Configuration file permissions are secure"
else
    echo "❌ Configuration file permissions are too open: $CONFIG_PERMS"
fi

echo -e "\n=== VULNERABILITY ASSESSMENT COMPLETED ==="
```

### 7.3 Compliance Verification

#### ✅ Documentation Compliance
- [ ] **Security policies** documentadas y aprobadas
- [ ] **Procedures de incidentes** definidos y probados
- [ ] **Training de seguridad** completado por el equipo
- [ ] **Risk assessment** documentado y mitigation plans implementados
- [ ] **Audit trail** completo y verificado
- [ ] **Data classification** definida y aplicada

#### ✅ Regulatory Compliance
- [ ] **GDPR Article 32** - Security of processing implementado
- [ ] **LOPD-GDD compliance** verificado
- [ ] **ISO 27001:2013** controles aplicables implementados
- [ ] **OWASP Database Security** top risks addressed
- [ ] **Industry best practices** aplicadas

---

## 📋 Checklist de Firma y Aprobación

### Verificación de Cumplimiento Técnico
**Database Administrator:** Carlos Martínez  
**Verificaciones Completadas:**
- [ ] Configuración PostgreSQL segura
- [ ] Usuarios y permisos configurados
- [ ] Auditoría y logging implementados
- [ ] Backup cifrado funcional
- [ ] Monitoring de seguridad activo

**Firma:** ________________  
**Fecha:** __/__/____

### Validación de Cumplimiento Normativo
**DevOps/Security Lead:** Miguel Torres  
**Compliance Verificado:**
- [ ] GDPR/LOPD requirements
- [ ] ISO 27001:2013 controls
- [ ] Security policies implemented
- [ ] Incident response procedures
- [ ] Documentation complete

**Firma:** ________________  
**Fecha:** __/__/____

### Aprobación de Seguridad de Aplicación
**Backend Lead Developer:** Ana García  
**Application Security:**
- [ ] Secure coding practices applied
- [ ] Input validation implemented
- [ ] Authentication/authorization secure
- [ ] Data encryption implemented
- [ ] Security testing completed

**Firma:** ________________  
**Fecha:** __/__/____

### Validación de Testing de Seguridad
**QA Lead:** Laura Pérez  
**Security Testing:**
- [ ] Penetration testing executed
- [ ] Vulnerability assessment completed
- [ ] Security test cases passed
- [ ] Performance under security load verified
- [ ] Documentation testing completed

**Firma:** ________________  
**Fecha:** __/__/____

---

## 📚 Referencias de Seguridad

### Standards y Frameworks
- **GDPR (EU) 2016/679** - General Data Protection Regulation
- **LOPD-GDD (España)** - Ley Orgánica de Protección de Datos
- **ISO/IEC 27001:2013** - Information Security Management
- **OWASP Database Security Project** - Database Security Guidelines
- **NIST Cybersecurity Framework** - Security Best Practices
- **PostgreSQL Security Documentation** - Official Security Guide

### Herramientas de Seguridad Utilizadas
- **pg_audit** - PostgreSQL Audit Extension
- **SSL/TLS** - Transport Layer Security
- **GnuPG** - Backup Encryption
- **iptables** - Network Security
- **fail2ban** - Intrusion Prevention
- **OSSEC** - Host-based Intrusion Detection

---

*Checklist de Cumplimiento de Seguridad para el Proyecto InmoTech - Sistema de Gestión Inmobiliaria*  
*Fase 1: Base de Datos y Migraciones | Enero 2026 | Equipo de Seguridad*