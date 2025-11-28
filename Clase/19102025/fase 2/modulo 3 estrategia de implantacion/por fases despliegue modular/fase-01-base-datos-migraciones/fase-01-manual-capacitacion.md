# Manual de Capacitación - Fase 1: Base de Datos y Migraciones

## 📋 Información del Proyecto
- **Proyecto:** InmoTech - Sistema de Gestión Inmobiliaria
- **Fase:** Fase 1 - Base de Datos y Migraciones
- **Dirigido a:** Equipo Técnico, DBAs, Desarrolladores Backend
- **Duración Estimada:** 2 días (16 horas académicas)
- **Instructor Principal:** Carlos Martínez - Database Administrator
- **Versión:** 1.0

---

## 🎯 Objetivos de la Capacitación

### Objetivo General
Preparar al equipo técnico para administrar, mantener y operar eficientemente la infraestructura de base de datos PostgreSQL implementada en la Fase 1 del proyecto InmoTech.

### Objetivos Específicos
- [ ] Comprender la arquitectura y diseño de la base de datos InmoTech
- [ ] Dominar los procedimientos de migración y rollback
- [ ] Aprender los procesos de backup y restauración
- [ ] Desarrollar habilidades de troubleshooting y mantenimiento
- [ ] Implementar monitoreo y optimización de rendimiento
- [ ] Conocer los procedimientos de seguridad y accesos

---

## 👥 Audiencia Objetivo

### Perfiles de Participantes

#### Database Administrator (DBA)
- **Nivel:** Avanzado
- **Responsabilidades:** Administración completa de la BD, optimización, seguridad
- **Tiempo de Capacitación:** 16 horas completas
- **Certificación Requerida:** Sí

#### Backend Developers
- **Nivel:** Intermedio
- **Responsabilidades:** Integración, queries, troubleshooting de aplicación
- **Tiempo de Capacitación:** 12 horas (módulos técnicos específicos)
- **Certificación Requerida:** Sí

#### DevOps Engineers
- **Nivel:** Intermedio
- **Responsabilidades:** Monitoreo, backup automatizado, deployment
- **Tiempo de Capacitación:** 10 horas (módulos de operación)
- **Certificación Requerida:** Sí

#### QA Testers
- **Nivel:** Básico-Intermedio
- **Responsabilidades:** Validación de datos, pruebas de rendimiento
- **Tiempo de Capacitación:** 6 horas (módulos básicos + testing)
- **Certificación Requerida:** No

---

## 📚 Módulo 1: Arquitectura de la Base de Datos InmoTech

### 1.1 Introducción al Diseño (2 horas)

#### Objetivo del Módulo
Comprender la arquitectura global y diseño relacional implementado para el sistema InmoTech.

#### Contenido Teórico (45 min)

**Historia y Contexto del Proyecto**
- Evolución del diseño de base de datos
- Requisitos de negocio que influyeron en el diseño
- Decisiones arquitectónicas clave (PostgreSQL vs otras opciones)

**Principios de Diseño Aplicados**
- Normalización hasta tercera forma normal (3NF)
- Estrategias de indexación implementadas
- Consideraciones de rendimiento vs integridad

**Diagrama Entidad-Relación (ERD)**
- Revisión completa del ERD actual
- Explicación de cada entidad y sus relaciones
- Cardinalidades y restricciones implementadas

#### Contenido Práctico (75 min)

**Exploración Hands-On de la Base de Datos**
```sql
-- Conexión a la base de datos
psql -h localhost -U inmotech_user -d inmotech

-- Exploración de estructura de tablas
\dt
\d users
\d properties
\d+ properties  -- Con información detallada
```

**Análisis de Relaciones**
```sql
-- Verificar foreign keys
SELECT 
    tc.table_name, 
    kcu.column_name, 
    ccu.table_name AS foreign_table_name,
    ccu.column_name AS foreign_column_name 
FROM information_schema.table_constraints tc
JOIN information_schema.key_column_usage kcu 
    ON tc.constraint_name = kcu.constraint_name
JOIN information_schema.constraint_column_usage ccu 
    ON ccu.constraint_name = tc.constraint_name
WHERE constraint_type = 'FOREIGN KEY';
```

**Verificación de Índices**
```sql
-- Revisar índices implementados
SELECT 
    tablename,
    indexname,
    indexdef
FROM pg_indexes
WHERE schemaname = 'public'
ORDER BY tablename, indexname;
```

#### Ejercicios Prácticos (1)
1. **Ejercicio 1:** Analizar la relación entre `users` y `properties`
2. **Ejercicio 2:** Identificar todos los índices de la tabla `messages`
3. **Ejercicio 3:** Explicar el diseño de la tabla `transactions` y sus implicaciones

#### Evaluación del Módulo
- [ ] **Quiz teórico:** Preguntas sobre el diseño ERD (15 min)
- [ ] **Evaluación práctica:** Navegación en la estructura de BD (15 min)

**Instructor:** Carlos Martínez  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado  
**Calificación Promedio:** ___/10

---

### 1.2 Tablas y Estructuras (2 horas)

#### Objetivo del Módulo
Dominar la estructura detallada de cada tabla, sus campos, tipos de datos y propósitos específicos.

#### Contenido Teórico (30 min)

**Tabla de Usuarios (users)**
- Estructura de campos y tipos de datos
- Estrategia de hashing de passwords
- Relación con roles y permisos
- Índices para optimización de búsquedas

**Tabla de Propiedades (properties)**
- Campos geográficos y su manejo (coordinates)
- Tipos de propiedades y estados
- Estrategias de búsqueda y filtrado
- Indexación por localización y precio

**Tabla de Transacciones (transactions)**
- Manejo de montos y precisión decimal
- Estados de transacciones y su ciclo de vida
- Auditoría y trazabilidad
- Relaciones con propiedades y usuarios

**Tablas de Comunicación (messages, notifications)**
- Diseño para escalabilidad
- Estados de lectura y notificación
- Índices para queries frecuentes
- Estrategias de purga y archivado

**Tablas Auxiliares (files, offers, roles)**
- Metadatos de archivos y su gestión
- Sistema de ofertas y expiración
- Gestión de roles y permisos granulares

#### Contenido Práctico (90 min)

**Deep Dive en Cada Tabla**
```sql
-- Análisis completo de la tabla users
SELECT 
    column_name,
    data_type,
    is_nullable,
    column_default,
    character_maximum_length
FROM information_schema.columns
WHERE table_name = 'users'
ORDER BY ordinal_position;

-- Análisis de constraints
SELECT 
    conname,
    contype,
    pg_get_constraintdef(c.oid) as definition
FROM pg_constraint c
JOIN pg_class t ON c.conrelid = t.oid
WHERE t.relname = 'users';
```

**Consultas de Ejemplo para Cada Tabla**
```sql
-- Consultas comunes en users
SELECT u.email, r.name as role 
FROM users u 
JOIN roles r ON u.role_id = r.id
WHERE u.created_at >= CURRENT_DATE - INTERVAL '30 days';

-- Consultas geográficas en properties
SELECT title, price, 
    ST_Distance(coordinates, ST_MakePoint(-74.0059, 40.7128)) as distance_km
FROM properties
WHERE ST_DWithin(coordinates, ST_MakePoint(-74.0059, 40.7128), 1000);

-- Análisis de rendimiento de transacciones
SELECT 
    DATE_TRUNC('month', transaction_date) as month,
    COUNT(*) as total_transactions,
    AVG(amount) as avg_amount,
    SUM(amount) as total_volume
FROM transactions
WHERE transaction_date >= CURRENT_DATE - INTERVAL '12 months'
GROUP BY DATE_TRUNC('month', transaction_date)
ORDER BY month;
```

#### Ejercicios Prácticos (2)
1. **Ejercicio 1:** Crear consultas optimizadas para cada tabla
2. **Ejercicio 2:** Analizar el plan de ejecución de consultas complejas
3. **Ejercicio 3:** Identificar oportunidades de optimización

#### Evaluación del Módulo
- [ ] **Práctica dirigida:** Construcción de queries complejas (20 min)
- [ ] **Análisis de rendimiento:** Uso de EXPLAIN ANALYZE (20 min)

**Instructor:** Carlos Martínez  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado  
**Calificación Promedio:** ___/10

---

## 🔄 Módulo 2: Procedimientos de Migración

### 2.1 Scripts de Migración (3 horas)

#### Objetivo del Módulo
Dominar completamente los procesos de migración de base de datos, incluyendo ejecución, validación y rollback.

#### Contenido Teórico (45 min)

**Filosofía de Migraciones**
- Versionado de base de datos y control de cambios
- Estrategias de migración: big bang vs incremental
- Consideraciones de downtime y disponibilidad

**Estructura de Scripts**
- Convenciones de naming y versionado
- Estructura de archivos up/down
- Metadatos y tracking de migraciones

**Herramientas y Framework**
- Scripts personalizados vs herramientas existentes
- Integración con Node.js y Sequelize
- Automación y CI/CD pipelines

#### Contenido Práctico (2h 15min)

**Ejecución de Migraciones Paso a Paso**
```bash
# Directorio de migraciones
cd /path/to/migrations/

# Verificar estado actual
node migration-status.js

# Ejecutar migración específica
node migrate.js --target 20260106_create_base_tables

# Verificar éxito de migración
node migration-status.js
psql -d inmotech -c "SELECT * FROM migration_versions;"
```

**Análisis de Scripts de Migración**
```sql
-- Ejemplo de script up migration
-- File: 20260106_create_base_tables_up.sql

BEGIN;

-- Crear tabla users
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    role_id INTEGER REFERENCES roles(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Crear índices
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role_id ON users(role_id);
CREATE INDEX idx_users_created_at ON users(created_at);

-- Insertar versión de migración
INSERT INTO migration_versions (version, applied_at) 
VALUES ('20260106_create_base_tables', CURRENT_TIMESTAMP);

COMMIT;
```

**Procedimientos de Rollback**
```sql
-- Ejemplo de script down migration
-- File: 20260106_create_base_tables_down.sql

BEGIN;

-- Eliminar tabla (cuidado con foreign keys)
DROP TABLE IF EXISTS users CASCADE;

-- Remover versión de migración
DELETE FROM migration_versions 
WHERE version = '20260106_create_base_tables';

COMMIT;
```

#### Ejercicios Prácticos (3)
1. **Ejercicio 1:** Ejecutar migración completa desde cero
2. **Ejercicio 2:** Simular rollback y recuperación
3. **Ejercicio 3:** Crear script de migración personalizado

#### Evaluación del Módulo
- [ ] **Práctica supervisada:** Ejecución de migración completa (30 min)
- [ ] **Resolución de problemas:** Troubleshooting de migración fallida (30 min)

**Instructor:** Carlos Martínez + Ana García  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado  
**Calificación Promedio:** ___/10

---

## 💾 Módulo 3: Backup y Restauración

### 3.1 Procedimientos de Backup (2 horas)

#### Objetivo del Módulo
Dominar todos los aspectos de backup y restauración, incluyendo procedimientos manuales y automatizados.

#### Contenido Teórico (30 min)

**Tipos de Backup**
- Full backup vs incremental
- Hot backup vs cold backup
- Point-in-time recovery (PITR)

**Estrategias de Backup**
- Frecuencia y timing
- Rotación y retención
- Storage y distribución geográfica

**Herramientas PostgreSQL**
- pg_dump y pg_dumpall
- pg_basebackup
- WAL archiving

#### Contenido Práctico (90 min)

**Backup Manual Completo**
```bash
# Backup completo de la base de datos
pg_dump -h localhost -U postgres -d inmotech \
    --no-password --verbose --clean --create \
    --format=custom \
    --file=inmotech_backup_$(date +%Y%m%d_%H%M%S).backup

# Verificar integridad del backup
pg_restore --list inmotech_backup_20260109_143022.backup

# Backup en formato SQL plano
pg_dump -h localhost -U postgres -d inmotech \
    --no-password --verbose --clean --create \
    --file=inmotech_backup_$(date +%Y%m%d_%H%M%S).sql
```

**Backup de Tablas Específicas**
```bash
# Backup solo de datos críticos
pg_dump -h localhost -U postgres -d inmotech \
    --table=users --table=properties --table=transactions \
    --data-only \
    --file=inmotech_critical_data_$(date +%Y%m%d_%H%M%S).sql

# Backup solo de estructura (schema)
pg_dump -h localhost -U postgres -d inmotech \
    --schema-only \
    --file=inmotech_schema_$(date +%Y%m%d_%H%M%S).sql
```

**Configuración de Backup Automatizado**
```bash
#!/bin/bash
# Script: backup_inmotech.sh

# Variables de configuración
DB_NAME="inmotech"
BACKUP_DIR="/var/backups/postgresql"
RETENTION_DAYS=7

# Crear directorio si no existe
mkdir -p $BACKUP_DIR

# Generar nombre del archivo
BACKUP_FILE="$BACKUP_DIR/inmotech_$(date +%Y%m%d_%H%M%S).backup"

# Ejecutar backup
pg_dump -h localhost -U postgres -d $DB_NAME \
    --format=custom --compress=9 \
    --file="$BACKUP_FILE"

# Verificar éxito
if [ $? -eq 0 ]; then
    echo "Backup exitoso: $BACKUP_FILE"
    # Limpiar backups antiguos
    find $BACKUP_DIR -name "inmotech_*.backup" -mtime +$RETENTION_DAYS -delete
else
    echo "Error en backup"
    exit 1
fi
```

#### Ejercicios Prácticos (4)
1. **Ejercicio 1:** Crear backup completo manual
2. **Ejercicio 2:** Configurar script de backup automatizado
3. **Ejercicio 3:** Implementar rotación de backups

**Instructor:** Miguel Torres + Carlos Martínez  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado

---

### 3.2 Procedimientos de Restauración (2 horas)

#### Contenido Práctico (2h)

**Restauración Completa**
```bash
# Restaurar desde backup custom format
pg_restore -h localhost -U postgres -d inmotech_restored \
    --clean --create --verbose \
    inmotech_backup_20260109_143022.backup

# Restaurar desde backup SQL
psql -h localhost -U postgres -f inmotech_backup_20260109_143022.sql

# Verificar integridad post-restauración
psql -d inmotech_restored -c "
    SELECT 
        'users' as table_name, COUNT(*) as records FROM users
    UNION ALL
    SELECT 
        'properties' as table_name, COUNT(*) as records FROM properties
    UNION ALL
    SELECT 
        'transactions' as table_name, COUNT(*) as records FROM transactions;
"
```

**Restauración Selectiva**
```bash
# Restaurar solo tabla específica
pg_restore -h localhost -U postgres -d inmotech \
    --table=properties --data-only \
    inmotech_backup_20260109_143022.backup

# Restaurar con exclusiones
pg_restore -h localhost -U postgres -d inmotech \
    --exclude-table=temp_* --exclude-table=logs_* \
    inmotech_backup_20260109_143022.backup
```

#### Ejercicios Prácticos (5)
1. **Ejercicio 1:** Restauración completa en nuevo ambiente
2. **Ejercicio 2:** Restauración selectiva de datos
3. **Ejercicio 3:** Recuperación ante corrupción simulada

**Instructor:** Miguel Torres + Carlos Martínez  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado

---

## ⚡ Módulo 4: Rendimiento y Optimización

### 4.1 Análisis de Rendimiento (3 horas)

#### Objetivo del Módulo
Desarrollar habilidades para identificar, analizar y resolver problemas de performance en la base de datos.

#### Contenido Teórico (45 min)

**Fundamentos de Performance**
- Factores que afectan performance: CPU, memoria, I/O
- Métricas clave: latency, throughput, concurrencia
- PostgreSQL query planner y optimizer

**Herramientas de Análisis**
- EXPLAIN y EXPLAIN ANALYZE
- pg_stat_statements
- pg_stat_activity
- Monitoring tools externos

#### Contenido Práctico (2h 15min)

**Uso de EXPLAIN ANALYZE**
```sql
-- Análisis básico de query
EXPLAIN ANALYZE
SELECT u.email, p.title, p.price
FROM users u
JOIN properties p ON u.id = p.user_id
WHERE u.created_at >= '2026-01-01'
AND p.price BETWEEN 100000 AND 500000;

-- Análisis con buffers
EXPLAIN (ANALYZE, BUFFERS)
SELECT p.*, 
       COUNT(m.id) as message_count,
       AVG(o.amount) as avg_offer
FROM properties p
LEFT JOIN messages m ON p.id = m.property_id
LEFT JOIN offers o ON p.id = o.property_id
WHERE p.status = 'active'
GROUP BY p.id
HAVING COUNT(m.id) > 5;
```

**Uso de pg_stat_statements**
```sql
-- Habilitar extensión (si no está activa)
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;

-- Queries más lentas
SELECT 
    query,
    total_time,
    calls,
    mean_time,
    rows
FROM pg_stat_statements
ORDER BY mean_time DESC
LIMIT 10;

-- Queries más frecuentes
SELECT 
    query,
    calls,
    total_time,
    rows,
    100.0 * shared_blks_hit / nullif(shared_blks_hit + shared_blks_read, 0) AS hit_percent
FROM pg_stat_statements
ORDER BY calls DESC
LIMIT 10;
```

**Optimización de Índices**
```sql
-- Identificar índices no utilizados
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY schemaname, tablename;

-- Identificar índices que necesitan rebuilding
SELECT 
    schemaname,
    tablename,
    indexname,
    pg_size_pretty(pg_relation_size(indexrelname::regclass)) as index_size,
    idx_scan,
    idx_tup_read
FROM pg_stat_user_indexes
ORDER BY pg_relation_size(indexrelname::regclass) DESC;

-- Crear índice optimizado
CREATE INDEX CONCURRENTLY idx_properties_location_price 
ON properties (location, price) 
WHERE status = 'active';
```

#### Ejercicios Prácticos (6)
1. **Ejercicio 1:** Identificar query más lenta del sistema
2. **Ejercicio 2:** Optimizar query mediante índices
3. **Ejercicio 3:** Analizar y optimizar consultas JOIN complejas

#### Evaluación del Módulo
- [ ] **Análisis práctico:** Optimización de query real (45 min)
- [ ] **Presentación:** Explicar plan de ejecución (15 min)

**Instructor:** Carlos Martínez  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado  
**Calificación Promedio:** ___/10

---

## 🔧 Módulo 5: Troubleshooting y Mantenimiento

### 5.1 Resolución de Problemas Comunes (2 horas)

#### Objetivo del Módulo
Preparar al equipo para identificar, diagnosticar y resolver problemas comunes en la operación diaria.

#### Contenido Teórico (30 min)

**Categorías de Problemas**
- Problemas de conectividad
- Issues de performance
- Problemas de espacio en disco
- Locks y deadlocks
- Corrupción de datos

**Metodología de Troubleshooting**
- Proceso sistemático de diagnóstico
- Uso de logs para identificar problemas
- Herramientas de monitoreo en tiempo real

#### Contenido Práctico (90 min)

**Diagnóstico de Conectividad**
```sql
-- Ver conexiones actuales
SELECT 
    pid,
    usename,
    application_name,
    client_addr,
    backend_start,
    state,
    query
FROM pg_stat_activity
WHERE state = 'active';

-- Identificar conexiones bloqueadas
SELECT 
    blocked_locks.pid AS blocked_pid,
    blocked_activity.usename AS blocked_user,
    blocking_locks.pid AS blocking_pid,
    blocking_activity.usename AS blocking_user,
    blocked_activity.query AS blocked_statement,
    blocking_activity.query AS blocking_statement
FROM pg_catalog.pg_locks blocked_locks
JOIN pg_catalog.pg_stat_activity blocked_activity 
    ON blocked_activity.pid = blocked_locks.pid
JOIN pg_catalog.pg_locks blocking_locks 
    ON blocking_locks.locktype = blocked_locks.locktype
    AND blocking_locks.database IS NOT DISTINCT FROM blocked_locks.database
    AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
    AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
    AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
    AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
    AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
    AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
    AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
    AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
    AND blocking_locks.pid != blocked_locks.pid
JOIN pg_catalog.pg_stat_activity blocking_activity 
    ON blocking_activity.pid = blocking_locks.pid
WHERE NOT blocked_locks.granted;
```

**Monitoreo de Espacio y Rendimiento**
```sql
-- Verificar espacio utilizado por tablas
SELECT 
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) as total_size,
    pg_size_pretty(pg_relation_size(schemaname||'.'||tablename)) as table_size,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename) - pg_relation_size(schemaname||'.'||tablename)) as index_size
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;

-- Verificar bloat de tablas
WITH constants AS (
    SELECT current_setting('block_size')::numeric AS bs, 23 AS hdr, 4 AS ma
), 
bloat_info AS (
    SELECT
        ma,bs,schemaname,tablename,
        (datawidth+(hdr+ma-(case when hdr%ma=0 THEN ma ELSE hdr%ma END)))::numeric AS datahdr,
        (maxfracsum*(nullhdr+ma-(case when nullhdr%ma=0 THEN ma ELSE nullhdr%ma END))) AS nullhdr2
    FROM (
        SELECT
            schemaname, tablename, hdr, ma, bs,
            SUM((1-null_frac)*avg_width) AS datawidth,
            MAX(null_frac) AS maxfracsum,
            hdr+(
                SELECT 1+count(*)/8
                FROM pg_stats s2
                WHERE null_frac<>0 AND s2.schemaname = s.schemaname AND s2.tablename = s.tablename
            ) AS nullhdr
        FROM pg_stats s, constants
        GROUP BY 1,2,3,4,5
    ) AS foo
)
SELECT
    schemaname, tablename,
    cc.relpages, bs,
    CEIL((cc.reltuples*((datahdr+ma-
        (CASE WHEN datahdr%ma=0 THEN ma ELSE datahdr%ma END))+nullhdr2+4))/(bs-20::float)) AS otta,
    COALESCE(c2.relname,'?') as iname, COALESCE(c2.relpages,0) as ipages, COALESCE(ceil(c2.reltuples/3.0),0) as iotta
FROM bloat_info
JOIN pg_class cc ON cc.relname = bloat_info.tablename
JOIN pg_namespace nn ON cc.relnamespace = nn.oid AND nn.nspname = bloat_info.schemaname AND nn.nspname <> 'information_schema'
LEFT JOIN pg_index i ON indrelid = cc.oid
LEFT JOIN pg_class c2 ON c2.oid = i.indexrelid
WHERE cc.relkind = 'r'
ORDER BY COALESCE(CEIL((cc.reltuples*((datahdr+ma-
    (CASE WHEN datahdr%ma=0 THEN ma ELSE datahdr%ma END))+nullhdr2+4))/(bs-20::float)),0) DESC;
```

#### Ejercicios Prácticos (7)
1. **Ejercicio 1:** Diagnosticar conexión lenta
2. **Ejercicio 2:** Resolver deadlock simulado
3. **Ejercicio 3:** Liberar espacio en disco crítico

**Instructor:** Carlos Martínez + Miguel Torres  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado

---

## 🔒 Módulo 6: Seguridad y Permisos

### 6.1 Configuración de Seguridad (2 horas)

#### Objetivo del Módulo
Implementar y mantener las mejores prácticas de seguridad para la base de datos InmoTech.

#### Contenido Teórico (45 min)

**Principios de Seguridad**
- Principio de menor privilegio
- Segregación de funciones
- Autenticación vs autorización
- Auditoría y logging

**Configuración de PostgreSQL**
- pg_hba.conf y autenticación
- postgresql.conf para seguridad
- SSL/TLS configuration
- Encryption at rest

#### Contenido Práctico (75 min)

**Configuración de Usuarios y Roles**
```sql
-- Crear usuarios específicos con permisos limitados
CREATE USER inmotech_app WITH PASSWORD 'secure_password_here';
CREATE USER inmotech_readonly WITH PASSWORD 'readonly_password_here';
CREATE USER inmotech_backup WITH PASSWORD 'backup_password_here';

-- Asignar permisos básicos
GRANT CONNECT ON DATABASE inmotech TO inmotech_app;
GRANT USAGE ON SCHEMA public TO inmotech_app;

-- Permisos granulares por tabla
GRANT SELECT, INSERT, UPDATE ON users TO inmotech_app;
GRANT SELECT, INSERT, UPDATE, DELETE ON properties TO inmotech_app;
GRANT SELECT, INSERT, UPDATE ON transactions TO inmotech_app;

-- Usuario de solo lectura
GRANT CONNECT ON DATABASE inmotech TO inmotech_readonly;
GRANT USAGE ON SCHEMA public TO inmotech_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO inmotech_readonly;

-- Usuario de backup
GRANT CONNECT ON DATABASE inmotech TO inmotech_backup;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO inmotech_backup;
```

**Configuración de pg_hba.conf**
```bash
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# Local connections
local   all             postgres                                peer
local   inmotech        inmotech_app                           md5

# IPv4 connections
host    inmotech        inmotech_app    10.0.0.0/8             md5
host    inmotech        inmotech_readonly 10.0.0.0/8           md5

# SSL required for external connections
hostssl inmotech        all             0.0.0.0/0               md5
```

#### Ejercicios Prácticos (8)
1. **Ejercicio 1:** Configurar usuario de aplicación con permisos mínimos
2. **Ejercicio 2:** Implementar segregación de roles
3. **Ejercicio 3:** Configurar SSL para conexiones remotas

**Instructor:** Carlos Martínez  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado

---

## 📊 Módulo 7: Monitoreo y Alertas

### 7.1 Implementación de Monitoreo (2 horas)

#### Objetivo del Módulo
Establecer un sistema completo de monitoreo y alertas para la base de datos PostgreSQL.

#### Contenido Teórico (30 min)

**Métricas Clave**
- Performance metrics: TPS, latency, query time
- Resource metrics: CPU, memory, disk I/O
- Health metrics: connection pool, locks, errors

**Herramientas de Monitoreo**
- PostgreSQL built-in statistics
- pgAdmin monitoring
- External monitoring solutions
- Custom alerting scripts

#### Contenido Práctico (90 min)

**Setup de Monitoreo Básico**
```sql
-- Habilitar estadísticas detalladas
ALTER SYSTEM SET track_activities = on;
ALTER SYSTEM SET track_counts = on;
ALTER SYSTEM SET track_io_timing = on;
ALTER SYSTEM SET track_functions = 'pl';
SELECT pg_reload_conf();

-- Script de monitoreo de métricas clave
SELECT 
    'Database Size' as metric,
    pg_size_pretty(pg_database_size('inmotech')) as value
UNION ALL
SELECT 
    'Active Connections' as metric,
    COUNT(*)::text as value
FROM pg_stat_activity
WHERE state = 'active'
UNION ALL
SELECT 
    'Longest Running Query' as metric,
    EXTRACT(EPOCH FROM (now() - query_start))::text || ' seconds' as value
FROM pg_stat_activity
WHERE state = 'active'
ORDER BY query_start
LIMIT 1;
```

**Scripts de Alertas**
```bash
#!/bin/bash
# Script: check_db_health.sh

# Verificar conexiones
ACTIVE_CONNECTIONS=$(psql -t -d inmotech -c "SELECT COUNT(*) FROM pg_stat_activity WHERE state = 'active'")
if [ $ACTIVE_CONNECTIONS -gt 50 ]; then
    echo "ALERT: High number of active connections: $ACTIVE_CONNECTIONS"
fi

# Verificar espacio en disco
DB_SIZE=$(psql -t -d inmotech -c "SELECT pg_database_size('inmotech')")
if [ $DB_SIZE -gt 10737418240 ]; then  # 10GB
    echo "ALERT: Database size is getting large: $(($DB_SIZE / 1024 / 1024 / 1024))GB"
fi

# Verificar queries lentas
SLOW_QUERIES=$(psql -t -d inmotech -c "SELECT COUNT(*) FROM pg_stat_activity WHERE state = 'active' AND now() - query_start > interval '30 seconds'")
if [ $SLOW_QUERIES -gt 0 ]; then
    echo "ALERT: $SLOW_QUERIES slow queries detected"
fi
```

#### Ejercicios Prácticos (9)
1. **Ejercicio 1:** Configurar dashboard de métricas básicas
2. **Ejercicio 2:** Implementar alertas automáticas
3. **Ejercicio 3:** Crear reportes de performance periódicos

**Instructor:** Miguel Torres + Carlos Martínez  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado

---

## 🔗 Módulo 8: Integración con Backend

### 8.1 Conexión y ORM (1.5 horas)

#### Objetivo del Módulo
Comprender la integración entre PostgreSQL y la aplicación Node.js utilizando Sequelize ORM.

#### Contenido Teórico (20 min)

**Sequelize ORM**
- Configuración y connection pooling
- Modelos y asociaciones
- Migraciones automáticas vs manuales
- Performance considerations

#### Contenido Práctico (70 min)

**Configuración de Conexión**
```javascript
// config/database.js
const { Sequelize } = require('sequelize');

const sequelize = new Sequelize({
    database: 'inmotech',
    username: 'inmotech_app',
    password: process.env.DB_PASSWORD,
    host: 'localhost',
    dialect: 'postgres',
    logging: console.log,
    pool: {
        max: 10,
        min: 0,
        acquire: 30000,
        idle: 10000
    }
});

module.exports = sequelize;
```

**Definición de Modelos**
```javascript
// models/User.js
const { DataTypes } = require('sequelize');
const sequelize = require('../config/database');

const User = sequelize.define('User', {
    email: {
        type: DataTypes.STRING,
        allowNull: false,
        unique: true,
        validate: {
            isEmail: true
        }
    },
    passwordHash: {
        type: DataTypes.STRING,
        allowNull: false,
        field: 'password_hash'
    },
    firstName: {
        type: DataTypes.STRING,
        allowNull: false,
        field: 'first_name'
    },
    lastName: {
        type: DataTypes.STRING,
        allowNull: false,
        field: 'last_name'
    }
}, {
    tableName: 'users',
    underscored: true,
    timestamps: true,
    createdAt: 'created_at',
    updatedAt: 'updated_at'
});

module.exports = User;
```

#### Ejercicios Prácticos (10)
1. **Ejercicio 1:** Configurar conexión Sequelize
2. **Ejercicio 2:** Crear modelo Property con asociaciones

**Instructor:** Ana García  
**Nivel de Completitud:** ⏳ Pendiente | ✅ Completado

---

## 📋 Evaluación Final y Certificación

### Examen Teórico (1 hora)

#### Estructura del Examen
- **Preguntas de opción múltiple:** 20 preguntas (40 puntos)
- **Preguntas de desarrollo:** 3 preguntas (30 puntos)
- **Análisis de caso:** 1 caso práctico (30 puntos)

#### Temas del Examen
1. **Arquitectura de base de datos InmoTech** (20%)
2. **Procedimientos de migración y rollback** (20%)
3. **Backup y restauración** (20%)
4. **Performance y optimización** (20%)
5. **Seguridad y troubleshooting** (20%)

### Evaluación Práctica (2 horas)

#### Ejercicio Final Integrador
**Scenario:** Migración de emergencia con rollback y optimización

**Tareas a Realizar:**
1. **Ejecutar migración** con datos simulados (30 min)
2. **Simular fallo** y ejecutar rollback (30 min)
3. **Identificar y optimizar** query lenta (30 min)
4. **Implementar backup** y validar restauración (30 min)

### Criterios de Certificación

#### Niveles de Certificación

**PostgreSQL InmoTech - Nivel Básico**
- **Puntuación mínima:** 70/100
- **Válido para:** QA Testers, Developers Junior
- **Módulos requeridos:** 1, 2, 6, 8

**PostgreSQL InmoTech - Nivel Intermedio**
- **Puntuación mínima:** 80/100  
- **Válido para:** Backend Developers, DevOps Engineers
- **Módulos requeridos:** 1, 2, 3, 4, 6, 7, 8

**PostgreSQL InmoTech - Nivel Avanzado**
- **Puntuación mínima:** 90/100
- **Válido para:** DBAs, Senior Developers, Tech Leads
- **Módulos requeridos:** Todos los módulos

### Registro de Certificaciones

| Participante | Rol | Nivel | Puntuación | Fecha | Certificador |
|--------------|-----|-------|------------|-------|-------------|
| [Nombre] | [Rol] | [Nivel] | [XX/100] | [DD/MM/YY] | [Instructor] |
| | | | | | |
| | | | | | |

---

## 📚 Recursos Adicionales

### Documentación de Referencia
- **PostgreSQL 14 Official Documentation:** https://postgresql.org/docs/14/
- **Sequelize Documentation:** https://sequelize.org/docs/
- **InmoTech ERD:** `documentacion/database-design/inmotech-erd-v2.1.pdf`
- **Scripts de Migración:** `database/migrations/`

### Herramientas Recomendadas
- **pgAdmin 4:** Interface gráfica de administración
- **DBeaver:** Cliente SQL universal
- **pg_stat_statements:** Análisis de performance
- **Postman:** Testing de APIs que interactúan con la DB

### Material de Estudio Complementario
- **PostgreSQL Performance Tuning Guide**
- **Database Security Best Practices**
- **Backup and Recovery Strategies**
- **Monitoring and Alerting Setup Guide**

### Scripts de Práctica
```bash
# Directorio de scripts de capacitación
/training/scripts/
├── setup_practice_env.sh
├── generate_test_data.sql
├── performance_tests.sql
├── backup_practice.sh
└── troubleshooting_scenarios.sql
```

---

## 📞 Soporte Post-Capacitación

### Canal de Soporte
- **Slack Channel:** #inmotech-db-support
- **Email:** database-support@inmotech.com
- **Horario:** Lunes a Viernes 9:00-18:00

### Office Hours
- **Instructor Principal:** Carlos Martínez
- **Horario:** Martes y Jueves 15:00-16:00
- **Modalidad:** Virtual (Teams)

### Recursos Continuos
- **Knowledge Base:** Wiki interno con casos resueltos
- **Monthly Review:** Revisión mensual de performance y mejores prácticas
- **Quarterly Updates:** Actualizaciones de procedimientos y optimizaciones

---

## ✅ Checklist de Completitud

### Pre-Capacitación
- [ ] Entorno de práctica configurado
- [ ] Accesos de base de datos otorgados
- [ ] Material didáctico distribuido
- [ ] Cronograma comunicado a participantes

### Durante la Capacitación
- [ ] Asistencia registrada por módulo
- [ ] Ejercicios prácticos completados
- [ ] Evaluaciones parciales aplicadas
- [ ] Dudas y consultas resueltas

### Post-Capacitación
- [ ] Examen final aplicado
- [ ] Certificaciones otorgadas
- [ ] Feedback recolectado
- [ ] Material de referencia entregado
- [ ] Canal de soporte activado

---

## 📊 Métricas de Efectividad

### KPIs de la Capacitación
- **Tasa de Completitud:** ___% (meta: >90%)
- **Promedio de Calificaciones:** ___/100 (meta: >85)
- **Tasa de Certificación:** ___% (meta: >95%)
- **Satisfacción del Participante:** ___/10 (meta: >8.5)

### Seguimiento Post-Capacitación
- **Incidentes relacionados con DB (mes 1):** ___
- **Tiempo de resolución promedio:** ___ minutos
- **Consultas de soporte semanales:** ___
- **Mejoras implementadas por el equipo:** ___

---

## 📝 Evaluación de la Capacitación

### Feedback de Participantes
[Espacio para recolectar feedback específico sobre cada módulo]

### Áreas de Mejora Identificadas
[Documentar áreas donde la capacitación puede mejorarse para futuras iteraciones]

### Recomendaciones para Próximas Capacitaciones
[Sugerencias para mejorar el programa de capacitación]

---

## ✅ Aprobaciones

### Aprobación del Contenido
**Instructor Principal:** Carlos Martínez  
**Firma:** ________________  
**Fecha:** __/__/____

### Aprobación Gerencial
**Project Manager:** Miguel Torres  
**Firma:** ________________  
**Fecha:** __/__/____

### Aprobación Técnica
**Backend Lead:** Ana García  
**Firma:** ________________  
**Fecha:** __/__/____

---

*Manual de Capacitación para el Proyecto InmoTech - Sistema de Gestión Inmobiliaria*  
*Fase 1: Base de Datos y Migraciones | Enero 2026 | Equipo de Capacitación*