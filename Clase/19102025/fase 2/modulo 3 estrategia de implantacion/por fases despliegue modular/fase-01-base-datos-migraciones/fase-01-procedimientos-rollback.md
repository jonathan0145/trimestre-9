# Procedimientos de Rollback - Fase 1: Base de Datos y Migraciones

## 📋 Información del Proyecto
- **Proyecto:** InmoTech - Sistema de Gestión Inmobiliaria
- **Fase:** Fase 1 - Base de Datos y Migraciones
- **Período de Aplicación:** 06/01/2026 - 10/01/2026
- **Responsable Principal:** Carlos Martínez - Database Administrator
- **Revisado por:** Miguel Torres (PM) + Ana García (Backend Lead)
- **Versión:** 1.0

---

## 🎯 Objetivos de los Procedimientos de Rollback

### Objetivo Principal
Establecer procedimientos claros, probados y documentados para revertir de manera segura y completa todos los cambios implementados durante la Fase 1, garantizando la integridad de datos y la disponibilidad del sistema.

### Objetivos Específicos
- [ ] Definir procedimientos de rollback para cada componente implementado
- [ ] Establecer criterios claros para activación de rollback
- [ ] Documentar scripts y comandos específicos para cada escenario
- [ ] Validar procedimientos mediante testing exhaustivo
- [ ] Asegurar tiempo de ejecución mínimo para rollback crítico

---

## 🚨 Criterios de Activación de Rollback

### Triggers Automáticos de Rollback

#### Criterios Críticos (Rollback Inmediato Obligatorio)
- **Corrupción de Datos:** Cualquier evidencia de pérdida o corrupción de datos
- **Fallo de Integridad:** Foreign keys o constraints violados sistemáticamente
- **Performance Crítico:** Queries principales > 2000ms consistentemente por > 30 min
- **Indisponibilidad Total:** Sistema completamente inaccesible por > 15 min
- **Error de Migración Fatal:** Script de migración que no puede completarse o corrige

#### Criterios de Evaluación (Rollback Considerado)
- **Performance Degradado:** Queries > 1000ms por > 60 min
- **Errores de Integración:** Backend no puede conectarse por > 30 min  
- **Fallos de Backup:** Sistema de backup no funcional por > 2 horas
- **Problemas de Escalabilidad:** Sistema no soporta carga mínima requerida

### Proceso de Decisión de Rollback

#### Responsables de Decisión
**Nivel 1 - Decisión Técnica Inmediata (0-15 min)**
- **Carlos Martínez (DBA):** Para issues de corrupción de datos o migración
- **Ana García (Backend Lead):** Para problemas de integración críticos
- **Miguel Torres (DevOps):** Para fallos de infraestructura

**Nivel 2 - Decisión de Project Management (15-30 min)**
- **Miguel Torres (PM):** Para impactos en cronograma o decisiones de scope
- **Equipo conjunto:** Para evaluaciones complejas que requieren múltiples perspectivas

**Nivel 3 - Decisión Ejecutiva (30+ min)**
- **Director Técnico:** Para decisiones que afectan el proyecto completo
- **Sponsor del Proyecto:** Para impactos en presupuesto o cronograma general

---

## 🔄 Tipos de Rollback Disponibles

### Rollback Tipo A: Rollback Completo de Fase
**Descripción:** Reversión completa de todos los cambios implementados en Fase 1  
**Tiempo de Ejecución:** 30-60 minutos  
**Downtime Requerido:** 45-75 minutos  
**Recuperación de Datos:** Desde backup pre-fase

### Rollback Tipo B: Rollback de Migración Específica  
**Descripción:** Reversión de una migración específica manteniendo cambios anteriores  
**Tiempo de Ejecución:** 10-30 minutos  
**Downtime Requerido:** 15-45 minutos  
**Recuperación de Datos:** Script de rollback específico

### Rollback Tipo C: Rollback de Configuración  
**Descripción:** Reversión de configuraciones de PostgreSQL sin afectar datos  
**Tiempo de Ejecución:** 5-15 minutos  
**Downtime Requerido:** 10-20 minutos  
**Recuperación de Datos:** No aplicable

### Rollback Tipo D: Rollback de Datos Semilla  
**Descripción:** Reversión únicamente de datos de prueba cargados  
**Tiempo de Ejecución:** 2-10 minutos  
**Downtime Requerido:** Mínimo (2-5 min)  
**Recuperación de Datos:** Eliminación selectiva

---

## 📋 Procedimiento General de Rollback

### Pre-Rollback (Preparación Inmediata)

#### Paso 1: Evaluación y Comunicación (0-5 min)
```bash
# Evaluar estado actual del sistema
echo "=== INICIO EVALUACIÓN ROLLBACK $(date) ===" | tee -a /var/log/rollback.log

# Verificar conexiones activas
psql -d inmotech -c "SELECT count(*) as active_connections FROM pg_stat_activity WHERE state = 'active';" | tee -a /var/log/rollback.log

# Notificar a stakeholders críticos
echo "ROLLBACK INICIADO - Fase 1 InmoTech" | mail -s "URGENT: Rollback en progreso" stakeholders@inmotech.com

# Activar modo mantenimiento (si aplicable)
touch /var/www/maintenance_mode
```

#### Paso 2: Verificación de Backup (5-10 min)
```bash
# Verificar que backup está disponible y es válido
BACKUP_FILE="/backups/inmotech_pre_phase1_$(date +%Y%m%d).backup"

if [ -f "$BACKUP_FILE" ]; then
    echo "Backup encontrado: $BACKUP_FILE" | tee -a /var/log/rollback.log
    
    # Verificar integridad del backup
    pg_restore --list "$BACKUP_FILE" > /dev/null 2>&1
    
    if [ $? -eq 0 ]; then
        echo "Backup verificado como válido" | tee -a /var/log/rollback.log
    else
        echo "ERROR: Backup corrupto - ABORTAR ROLLBACK" | tee -a /var/log/rollback.log
        exit 1
    fi
else
    echo "ERROR: Backup no encontrado - ABORTAR ROLLBACK" | tee -a /var/log/rollback.log
    exit 1
fi
```

#### Paso 3: Backup de Estado Actual (10-15 min)
```bash
# Crear backup del estado actual antes de rollback
CURRENT_BACKUP="/backups/inmotech_pre_rollback_$(date +%Y%m%d_%H%M%S).backup"

echo "Creando backup de estado actual..." | tee -a /var/log/rollback.log
pg_dump -h localhost -U postgres -d inmotech --format=custom --compress=9 --file="$CURRENT_BACKUP"

if [ $? -eq 0 ]; then
    echo "Backup de estado actual creado: $CURRENT_BACKUP" | tee -a /var/log/rollback.log
else
    echo "WARNING: No se pudo crear backup de estado actual" | tee -a /var/log/rollback.log
fi
```

---

## 🔄 Rollback Tipo A: Rollback Completo de Fase

### Cuando Usar
- Corrupción crítica de datos
- Fallo completo de migración
- Performance completamente inaceptable
- Múltiples sistemas afectados

### Procedimiento Detallado

#### Paso 1: Terminación de Conexiones (0-2 min)
```sql
-- Terminar todas las conexiones activas a la base de datos
SELECT pg_terminate_backend(pg_stat_activity.pid)
FROM pg_stat_activity
WHERE pg_stat_activity.datname = 'inmotech'
  AND pid <> pg_backend_pid();
```

#### Paso 2: Eliminación de Base de Datos (2-5 min)
```bash
# Eliminar base de datos actual completamente
echo "Eliminando base de datos actual..." | tee -a /var/log/rollback.log
dropdb -h localhost -U postgres inmotech

if [ $? -eq 0 ]; then
    echo "Base de datos eliminada exitosamente" | tee -a /var/log/rollback.log
else
    echo "ERROR: No se pudo eliminar base de datos" | tee -a /var/log/rollback.log
    exit 1
fi
```

#### Paso 3: Restauración desde Backup (5-25 min)
```bash
# Restaurar desde backup pre-fase
echo "Restaurando desde backup pre-fase..." | tee -a /var/log/rollback.log
pg_restore -h localhost -U postgres --clean --create --verbose "$BACKUP_FILE"

if [ $? -eq 0 ]; then
    echo "Restauración completada exitosamente" | tee -a /var/log/rollback.log
else
    echo "ERROR: Fallo en restauración" | tee -a /var/log/rollback.log
    exit 1
fi
```

#### Paso 4: Verificación Post-Rollback (25-35 min)
```bash
# Script de verificación completa
echo "Iniciando verificaciones post-rollback..." | tee -a /var/log/rollback.log

# Verificar conectividad
psql -d inmotech -c "SELECT 'Conexión exitosa' as status;" | tee -a /var/log/rollback.log

# Verificar tablas críticas
psql -d inmotech -c "
SELECT 
    'users' as table_name, 
    count(*) as records 
FROM users
UNION ALL
SELECT 
    'roles' as table_name, 
    count(*) as records 
FROM roles;" | tee -a /var/log/rollback.log

# Verificar integridad referencial
psql -d inmotech -c "
SELECT 
    conname,
    contype
FROM pg_constraint 
WHERE contype = 'f';" | tee -a /var/log/rollback.log
```

#### Paso 5: Comunicación de Completitud (35-40 min)
```bash
echo "=== ROLLBACK TIPO A COMPLETADO $(date) ===" | tee -a /var/log/rollback.log

# Notificar completitud
cat > /tmp/rollback_notification.txt << EOF
ROLLBACK COMPLETO EXITOSO

Proyecto: InmoTech Fase 1
Tipo: Rollback Completo de Fase
Inicio: $(head -1 /var/log/rollback.log)
Fin: $(date)
Estado: COMPLETADO
Base de datos: Restaurada a estado pre-Fase 1

Próximos pasos:
1. Validar funcionalidad completa
2. Analizar causa raíz del problema
3. Planificar re-implementación
EOF

mail -s "ROLLBACK COMPLETADO - InmoTech Fase 1" stakeholders@inmotech.com < /tmp/rollback_notification.txt
```

---

## 🎯 Rollback Tipo B: Rollback de Migración Específica

### Cuando Usar
- Fallo en migración específica
- Error en script de transformación de datos
- Problema con tabla o índice específico

### Scripts de Rollback por Migración

#### Rollback: 20260106_create_base_tables
```sql
-- Archivo: migrations/rollback/20260106_create_base_tables_down.sql
BEGIN;

-- Guardar información de foreign keys para recrear si es necesario
CREATE TEMP TABLE temp_fk_info AS
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
WHERE tc.constraint_type = 'FOREIGN KEY'
    AND tc.table_name IN ('users', 'roles', 'properties');

-- Eliminar tablas en orden correcto (respetando foreign keys)
DROP TABLE IF EXISTS files CASCADE;
DROP TABLE IF EXISTS offers CASCADE;
DROP TABLE IF EXISTS messages CASCADE;
DROP TABLE IF EXISTS notifications CASCADE;
DROP TABLE IF EXISTS transactions CASCADE;
DROP TABLE IF EXISTS properties CASCADE;
DROP TABLE IF EXISTS users CASCADE;
DROP TABLE IF EXISTS roles CASCADE;

-- Remover entrada de migración
DELETE FROM migration_versions WHERE version = '20260106_create_base_tables';

-- Log del rollback
INSERT INTO rollback_log (migration, rollback_date, rollback_by)
VALUES ('20260106_create_base_tables', CURRENT_TIMESTAMP, current_user);

COMMIT;
```

#### Rollback: 20260107_create_indexes
```sql
-- Archivo: migrations/rollback/20260107_create_indexes_down.sql
BEGIN;

-- Eliminar índices creados en esta migración
DROP INDEX IF EXISTS idx_users_email;
DROP INDEX IF EXISTS idx_users_role_id;
DROP INDEX IF EXISTS idx_users_created_at;
DROP INDEX IF EXISTS idx_properties_user_id;
DROP INDEX IF EXISTS idx_properties_type_status;
DROP INDEX IF EXISTS idx_properties_location_price;
DROP INDEX IF EXISTS idx_messages_sender_receiver;
DROP INDEX IF EXISTS idx_transactions_property_date;

-- Remover entrada de migración
DELETE FROM migration_versions WHERE version = '20260107_create_indexes';

-- Log del rollback
INSERT INTO rollback_log (migration, rollback_date, rollback_by)
VALUES ('20260107_create_indexes', CURRENT_TIMESTAMP, current_user);

COMMIT;
```

### Procedimiento de Ejecución de Rollback de Migración

#### Script de Ejecución Automática
```bash
#!/bin/bash
# Archivo: scripts/rollback_migration.sh

MIGRATION_NAME=$1

if [ -z "$MIGRATION_NAME" ]; then
    echo "ERROR: Debe especificar el nombre de la migración"
    echo "Uso: $0 <nombre_migración>"
    exit 1
fi

ROLLBACK_FILE="/migrations/rollback/${MIGRATION_NAME}_down.sql"

echo "Ejecutando rollback de migración: $MIGRATION_NAME"

# Verificar que el archivo de rollback existe
if [ ! -f "$ROLLBACK_FILE" ]; then
    echo "ERROR: Archivo de rollback no encontrado: $ROLLBACK_FILE"
    exit 1
fi

# Backup rápido antes del rollback
BACKUP_FILE="/tmp/pre_rollback_${MIGRATION_NAME}_$(date +%Y%m%d_%H%M%S).sql"
echo "Creando backup rápido..."
pg_dump -h localhost -U postgres -d inmotech --clean --schema-only > "$BACKUP_FILE"

# Ejecutar rollback
echo "Ejecutando script de rollback..."
psql -h localhost -U postgres -d inmotech -f "$ROLLBACK_FILE"

if [ $? -eq 0 ]; then
    echo "Rollback de migración $MIGRATION_NAME completado exitosamente"
else
    echo "ERROR: Fallo en rollback de migración"
    echo "Backup disponible en: $BACKUP_FILE"
    exit 1
fi

# Verificación post-rollback
echo "Verificando estado de base de datos..."
psql -d inmotech -c "SELECT version FROM migration_versions ORDER BY applied_at DESC LIMIT 5;"
```

---

## ⚙️ Rollback Tipo C: Rollback de Configuración

### Configuraciones a Revertir

#### PostgreSQL Configuration Rollback
```bash
#!/bin/bash
# Archivo: scripts/rollback_pg_config.sh

echo "Revirtiendo configuración de PostgreSQL..."

# Backup de configuración actual
cp /etc/postgresql/14/main/postgresql.conf /etc/postgresql/14/main/postgresql.conf.backup.$(date +%Y%m%d_%H%M%S)
cp /etc/postgresql/14/main/pg_hba.conf /etc/postgresql/14/main/pg_hba.conf.backup.$(date +%Y%m%d_%H%M%S)

# Restaurar configuración pre-fase
cp /backups/postgresql.conf.pre_phase1 /etc/postgresql/14/main/postgresql.conf
cp /backups/pg_hba.conf.pre_phase1 /etc/postgresql/14/main/pg_hba.conf

# Reiniciar PostgreSQL
systemctl restart postgresql

# Verificar reinicio exitoso
if systemctl is-active --quiet postgresql; then
    echo "PostgreSQL reiniciado exitosamente con configuración anterior"
else
    echo "ERROR: PostgreSQL no pudo reiniciarse"
    exit 1
fi

# Verificar conectividad
psql -d inmotech -c "SELECT 'Conexión exitosa' as status;"
```

#### Rollback de Usuarios y Permisos
```sql
-- Archivo: scripts/rollback_users_permissions.sql
BEGIN;

-- Eliminar usuarios creados en Fase 1
DROP USER IF EXISTS inmotech_app;
DROP USER IF EXISTS inmotech_readonly;
DROP USER IF EXISTS inmotech_backup;

-- Recrear usuario original (si aplicable)
CREATE USER inmotech_original WITH PASSWORD 'original_password';
GRANT ALL PRIVILEGES ON DATABASE inmotech TO inmotech_original;

COMMIT;
```

---

## 🗂️ Rollback Tipo D: Rollback de Datos Semilla

### Procedimiento de Limpieza de Datos

#### Script de Limpieza Selectiva
```sql
-- Archivo: scripts/rollback_seed_data.sql
BEGIN;

-- Eliminar datos semilla en orden correcto
DELETE FROM offers WHERE user_id IN (SELECT id FROM users WHERE email LIKE '%example.com%');
DELETE FROM messages WHERE sender_id IN (SELECT id FROM users WHERE email LIKE '%example.com%');
DELETE FROM files WHERE uploaded_by IN (SELECT id FROM users WHERE email LIKE '%example.com%');
DELETE FROM transactions WHERE buyer_id IN (SELECT id FROM users WHERE email LIKE '%example.com%');
DELETE FROM properties WHERE user_id IN (SELECT id FROM users WHERE email LIKE '%example.com%');
DELETE FROM users WHERE email LIKE '%example.com%';

-- Resetear secuencias
ALTER SEQUENCE users_id_seq RESTART WITH 1;
ALTER SEQUENCE properties_id_seq RESTART WITH 1;
ALTER SEQUENCE transactions_id_seq RESTART WITH 1;

-- Log de limpieza
INSERT INTO cleanup_log (operation, cleaned_at, cleaned_by)
VALUES ('seed_data_rollback', CURRENT_TIMESTAMP, current_user);

COMMIT;
```

---

## 🔍 Validaciones Post-Rollback

### Checklist de Validación Obligatorio

#### Validaciones Técnicas
- [ ] **Conectividad:** Base de datos acepta conexiones
- [ ] **Integridad:** Todas las foreign keys válidas
- [ ] **Performance:** Queries básicas < 100ms
- [ ] **Backup:** Sistema de backup funcional
- [ ] **Usuarios:** Permisos y accesos correctos

#### Validaciones Funcionales
- [ ] **Datos:** Conteos de registros coinciden con baseline
- [ ] **Estructura:** Todas las tablas esperadas presentes
- [ ] **Índices:** Índices críticos funcionando
- [ ] **Configuración:** Parámetros de PostgreSQL correctos

#### Validaciones de Integración
- [ ] **Backend:** Aplicación puede conectarse
- [ ] **ORM:** Sequelize funciona correctamente
- [ ] **Herramientas:** pgAdmin accesible
- [ ] **Monitoreo:** Alertas y monitoreo operativo

### Scripts de Validación Automática

#### Validación Completa Post-Rollback
```bash
#!/bin/bash
# Archivo: scripts/validate_post_rollback.sh

echo "=== INICIANDO VALIDACIÓN POST-ROLLBACK ===" | tee -a /var/log/rollback_validation.log

VALIDATION_FAILED=0

# Test 1: Conectividad básica
echo "Test 1: Conectividad básica"
psql -d inmotech -c "SELECT 'OK' as connectivity_test;" &>/dev/null
if [ $? -eq 0 ]; then
    echo "✅ Conectividad: PASS" | tee -a /var/log/rollback_validation.log
else
    echo "❌ Conectividad: FAIL" | tee -a /var/log/rollback_validation.log
    VALIDATION_FAILED=1
fi

# Test 2: Verificación de tablas
echo "Test 2: Verificación de tablas"
EXPECTED_TABLES=("users" "roles" "properties" "transactions" "messages" "notifications" "files" "offers")
for table in "${EXPECTED_TABLES[@]}"; do
    psql -d inmotech -c "\dt $table" &>/dev/null
    if [ $? -eq 0 ]; then
        echo "✅ Tabla $table: PASS" | tee -a /var/log/rollback_validation.log
    else
        echo "❌ Tabla $table: FAIL" | tee -a /var/log/rollback_validation.log
        VALIDATION_FAILED=1
    fi
done

# Test 3: Integridad referencial
echo "Test 3: Integridad referencial"
CONSTRAINT_VIOLATIONS=$(psql -t -d inmotech -c "
SELECT COUNT(*) FROM (
    SELECT u.id FROM users u LEFT JOIN roles r ON u.role_id = r.id WHERE u.role_id IS NOT NULL AND r.id IS NULL
    UNION ALL
    SELECT p.id FROM properties p LEFT JOIN users u ON p.user_id = u.id WHERE p.user_id IS NOT NULL AND u.id IS NULL
) violations;")

if [ "$CONSTRAINT_VIOLATIONS" -eq 0 ]; then
    echo "✅ Integridad referencial: PASS" | tee -a /var/log/rollback_validation.log
else
    echo "❌ Integridad referencial: FAIL ($CONSTRAINT_VIOLATIONS violaciones)" | tee -a /var/log/rollback_validation.log
    VALIDATION_FAILED=1
fi

# Test 4: Performance básico
echo "Test 4: Performance básico"
START_TIME=$(date +%s%N)
psql -d inmotech -c "SELECT COUNT(*) FROM users;" &>/dev/null
END_TIME=$(date +%s%N)
DURATION=$(( (END_TIME - START_TIME) / 1000000 )) # Convert to milliseconds

if [ "$DURATION" -lt 500 ]; then
    echo "✅ Performance ($DURATION ms): PASS" | tee -a /var/log/rollback_validation.log
else
    echo "⚠️ Performance ($DURATION ms): WARNING - más lento de lo esperado" | tee -a /var/log/rollback_validation.log
fi

# Resultado final
if [ $VALIDATION_FAILED -eq 0 ]; then
    echo "🎉 VALIDACIÓN POST-ROLLBACK: EXITOSA" | tee -a /var/log/rollback_validation.log
    echo "rollback_validation_status=PASS" > /tmp/rollback_status
else
    echo "🚨 VALIDACIÓN POST-ROLLBACK: FALLÓ" | tee -a /var/log/rollback_validation.log
    echo "rollback_validation_status=FAIL" > /tmp/rollback_status
fi

exit $VALIDATION_FAILED
```

---

## 📞 Comunicación Durante Rollback

### Templates de Comunicación

#### Notificación de Inicio de Rollback
```
ASUNTO: URGENTE - Inicio de Rollback - InmoTech Fase 1

Estimados stakeholders,

Se ha activado el procedimiento de rollback para la Fase 1 del proyecto InmoTech debido a: [RAZÓN]

DETALLES:
- Tipo de Rollback: [A/B/C/D]
- Tiempo estimado: [XX] minutos
- Downtime esperado: [XX] minutos
- Responsable: Carlos Martínez (DBA)

ESTADO ACTUAL: [INICIANDO/EN_PROGRESO/COMPLETANDO]

Mantendremos comunicación cada 15 minutos con updates de progreso.

Equipo de Proyecto InmoTech
```

#### Actualización de Progreso
```
ASUNTO: Update Rollback - InmoTech Fase 1 [XX% Completado]

STATUS UPDATE - Rollback en progreso

Tiempo transcurrido: [XX] minutos de [XX] estimados
Progreso: [XX%] completado
Estado actual: [DESCRIPCIÓN_DEL_PASO_ACTUAL]

Próximo milestone: [XX:XX]
Sin problemas reportados.

Equipo de Proyecto InmoTech
```

#### Notificación de Completitud
```
ASUNTO: COMPLETADO - Rollback Exitoso - InmoTech Fase 1

Estimados stakeholders,

El procedimiento de rollback ha sido COMPLETADO EXITOSAMENTE.

RESUMEN:
- Duración total: [XX] minutos
- Tipo ejecutado: Rollback [A/B/C/D]
- Estado del sistema: OPERATIVO
- Validaciones: TODAS PASARON

PRÓXIMOS PASOS:
1. Análisis de causa raíz (responsable: [NOMBRE])
2. Plan de corrección (fecha: [DD/MM])
3. Re-planificación de implementación

El sistema está disponible para uso normal.

Equipo de Proyecto InmoTech
```

---

## 📋 Testing y Validación de Procedimientos

### Cronograma de Testing de Rollback

#### Pre-Implementación (05/01/2026)
- **09:00-12:00:** Testing de Rollback Tipo A (completo)
- **13:00-15:00:** Testing de Rollback Tipo B (por migración)
- **15:00-16:00:** Testing de Rollback Tipo C (configuración)
- **16:00-17:00:** Validación de scripts y documentación

#### Durante Implementación
- **Testing en vivo:** Rollback Tipo D (datos semilla) - día 4
- **Validación continua:** Scripts de validación cada día

### Escenarios de Testing

#### Escenario 1: Rollback Completo bajo Presión
**Simulación:** Corrupción de datos críticos durante migración
**Tiempo límite:** 45 minutos
**Criterio de éxito:** Sistema completamente restaurado y funcional
**Participantes:** Carlos Martínez (lead) + Ana García + Miguel Torres

#### Escenario 2: Rollback Selectivo con Datos Activos
**Simulación:** Fallo en migración específica con transacciones activas
**Tiempo límite:** 30 minutos
**Criterio de éxito:** Migración revertida sin afectar datos existentes
**Participantes:** Carlos Martínez (lead) + Laura Pérez

#### Escenario 3: Rollback de Configuración en Producción
**Simulación:** Performance crítico por configuración incorrecta
**Tiempo límite:** 15 minutos
**Criterio de éxito:** Configuración revertida, performance restaurado
**Participantes:** Miguel Torres (lead) + Carlos Martínez

### Métricas de Validación

#### KPIs de Efectividad de Rollback
- **Tiempo de ejecución real vs estimado:** Meta ± 20%
- **Éxito en primera ejecución:** Meta > 95%
- **Integridad de datos post-rollback:** Meta 100%
- **Disponibilidad post-rollback:** Meta < 10 min downtime adicional

---

## 🔍 Troubleshooting de Problemas de Rollback

### Problemas Comunes y Soluciones

#### Problema 1: Backup Corrupto o Inaccesible
**Síntomas:**
- Error al validar backup con pg_restore --list
- Archivo de backup no encontrado
- Backup parcial o incompleto

**Diagnóstico:**
```bash
# Verificar existencia y permisos
ls -la /backups/inmotech_pre_phase1_*.backup
file /backups/inmotech_pre_phase1_*.backup

# Intentar listar contenido
pg_restore --list /backups/inmotech_pre_phase1_*.backup

# Verificar checksum si está disponible
sha256sum /backups/inmotech_pre_phase1_*.backup
```

**Solución:**
```bash
# Opción 1: Usar backup alternativo
BACKUP_ALT="/backups/secondary/inmotech_daily_$(date -d yesterday +%Y%m%d).backup"

# Opción 2: Restaurar desde replica si existe
pg_basebackup -h replica_server -D /tmp/replica_restore -U replication

# Opción 3: Rollback parcial manteniendo lo que funciona
# (Solo si es absolutamente necesario)
```

#### Problema 2: Scripts de Rollback Fallan
**Síntomas:**
- Error en ejecución de script SQL
- Foreign keys impiden eliminación de tablas
- Dependencias no resueltas

**Diagnóstico:**
```sql
-- Verificar dependencias activas
SELECT 
    tc.table_name,
    tc.constraint_name,
    tc.constraint_type,
    kcu.column_name,
    ccu.table_name AS references_table,
    ccu.column_name AS references_column
FROM information_schema.table_constraints tc
JOIN information_schema.key_column_usage kcu ON tc.constraint_name = kcu.constraint_name
JOIN information_schema.constraint_column_usage ccu ON ccu.constraint_name = tc.constraint_name
WHERE tc.constraint_type = 'FOREIGN KEY';
```

**Solución:**
```sql
-- Deshabilitar foreign keys temporalmente
SET session_replication_role = replica;

-- Ejecutar rollback
-- [SCRIPTS DE ROLLBACK]

-- Re-habilitar foreign keys
SET session_replication_role = DEFAULT;

-- Verificar integridad
SELECT * FROM pg_constraint WHERE contype = 'f';
```

#### Problema 3: Conexiones Activas Impiden Rollback
**Síntomas:**
- No se pueden terminar conexiones activas
- Database en uso por otros procesos
- Timeout en terminación de conexiones

**Solución:**
```bash
# Método agresivo de terminación
sudo systemctl stop postgresql
sleep 5
sudo systemctl start postgresql

# Verificar que no hay procesos huérfanos
ps aux | grep postgres | grep inmotech

# Ejecutar rollback inmediatamente después del restart
```

---

## ✅ Checklist de Preparación para Rollback

### Pre-Implementación (Debe completarse antes de iniciar Fase 1)

#### Preparación de Backups
- [ ] Backup completo pre-fase verificado y accesible
- [ ] Backup secundario en ubicación geográfica diferente
- [ ] Scripts de backup automatizado probados
- [ ] Checksums de backup calculados y verificados

#### Preparación de Scripts
- [ ] Scripts de rollback para cada tipo probados en staging
- [ ] Scripts de validación post-rollback funcionando
- [ ] Scripts de comunicación preparados y personalizados
- [ ] Documentación de scripts actualizada y accesible

#### Preparación del Equipo
- [ ] Equipo de rollback entrenado y certificado
- [ ] Contactos de emergencia actualizados y disponibles
- [ ] Comunicación con stakeholders establecida
- [ ] Roles y responsabilidades claramente definidos

#### Preparación de Infraestructura
- [ ] Servidores de backup configurados y probados
- [ ] Herramientas de monitoreo configuradas para rollback
- [ ] Accesos de emergencia verificados
- [ ] Logs centralizados y accesibles

### Durante Implementación

#### Monitoreo Continuo
- [ ] Dashboard de métricas críticas activo
- [ ] Alertas automáticas configuradas
- [ ] Logs siendo monitoreados en tiempo real
- [ ] Comunicación regular con stakeholders

#### Validación Periódica
- [ ] Checkpoints de validación cada 2 horas
- [ ] Backup incremental después de cada migración mayor
- [ ] Testing de conectividad backend cada hora
- [ ] Validación de performance cada 30 minutos

---

## 📊 Métricas y Reportes de Rollback

### KPIs de Proceso de Rollback

#### Métricas de Tiempo
- **Tiempo de Decisión:** Desde detección de problema hasta decisión de rollback
- **Tiempo de Preparación:** Desde decisión hasta inicio de rollback
- **Tiempo de Ejecución:** Desde inicio hasta completitud de rollback
- **Tiempo de Validación:** Desde completitud hasta validación completa

#### Métricas de Calidad
- **Integridad de Datos:** % de datos preservados correctamente
- **Disponibilidad:** % de uptime durante el proceso
- **Rollback Success Rate:** % de rollbacks exitosos en primera ejecución
- **Recovery Time:** Tiempo total de indisponibilidad

### Reporte Post-Rollback

#### Template de Reporte
```
REPORTE POST-ROLLBACK - INMOTECH FASE 1

=== INFORMACIÓN GENERAL ===
Fecha: [DD/MM/AAAA]
Hora Inicio: [HH:MM]
Hora Fin: [HH:MM]
Duración Total: [XXX] minutos
Tipo de Rollback: [A/B/C/D]

=== RAZÓN DEL ROLLBACK ===
Problema Principal: [DESCRIPCIÓN]
Severidad: [CRÍTICA/ALTA/MEDIA]
Impacto: [DESCRIPCIÓN_DEL_IMPACTO]

=== MÉTRICAS ===
Tiempo de Decisión: [XX] minutos
Tiempo de Ejecución: [XX] minutos
Downtime Total: [XX] minutos
Datos Afectados: [DESCRIPCIÓN]

=== VALIDACIONES ===
✅ Conectividad: PASS
✅ Integridad: PASS
✅ Performance: PASS
✅ Funcionalidad: PASS

=== LECCIONES APRENDIDAS ===
1. [LECCIÓN_1]
2. [LECCIÓN_2]
3. [LECCIÓN_3]

=== RECOMENDACIONES ===
1. [RECOMENDACIÓN_1]
2. [RECOMENDACIÓN_2]
3. [RECOMENDACIÓN_3]

=== PRÓXIMOS PASOS ===
1. [ACCIÓN_1] - Responsable: [NOMBRE] - Fecha: [DD/MM]
2. [ACCIÓN_2] - Responsable: [NOMBRE] - Fecha: [DD/MM]
3. [ACCIÓN_3] - Responsable: [NOMBRE] - Fecha: [DD/MM]

Preparado por: [NOMBRE]
Revisado por: [NOMBRE]
Aprobado por: [NOMBRE]
```

---

## 📚 Referencias y Documentación

### Documentos Relacionados
- **Plan de Implementación Fase 1:** `fase-01-plan-implementacion.md`
- **Análisis de Riesgos Fase 1:** `fase-01-analisis-riesgos.md`
- **Registro de Incidentes:** `fase-01-registro-incidentes.md`
- **Manual de Operaciones PostgreSQL:** `documentacion/postgresql-operations.md`

### Herramientas y Recursos
- **PostgreSQL Documentation:** https://postgresql.org/docs/14/
- **Backup Best Practices:** Internal Wiki
- **Emergency Contacts:** `contacts/emergency-team.md`
- **Script Repository:** `/scripts/rollback/`

---

## ✅ Aprobaciones

### Aprobación Técnica
**Responsable:** Carlos Martínez  
**Cargo:** Database Administrator  
**Firma:** ________________  
**Fecha:** __/__/____

### Aprobación de Proceso  
**Responsable:** Miguel Torres  
**Cargo:** Project Manager  
**Firma:** ________________  
**Fecha:** __/__/____

### Aprobación de Integración
**Responsable:** Ana García  
**Cargo:** Backend Lead Developer  
**Firma:** ________________  
**Fecha:** __/__/____

---

*Procedimientos de Rollback para el Proyecto InmoTech - Sistema de Gestión Inmobiliaria*  
*Fase 1: Base de Datos y Migraciones | Enero 2026 | Equipo de Operaciones*