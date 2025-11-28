# Plan de Migración de Datos - Fase 9: Archivos y Almacenamiento

## Información de la Fase

**Nombre de la Fase:** Archivos y Almacenamiento
**Número de Fase:** 9
**Fecha de Inicio:** 17/02/2026
**Fecha de Fin:** 24/02/2026
**Responsable de Migración:** Ricardo Fernández (DevOps Lead)
**Data Architect:** Carmen López (Backend Lead)

---

## 🎯 Objetivo de la Migración

### Propósito Principal
Migrar todos los archivos existentes del **Sistema InmoTech** desde el almacenamiento local actual hacia una **infraestructura cloud escalable** con AWS S3 + CDN, garantizando integridad de datos, optimización de performance y mejora en la experiencia de usuario.

### Alcance de la Migración
```yaml
Datos a Migrar:
  Fotos de propiedades: ~1.2TB, 450,000 archivos
  Videos de tours virtuales: ~800GB, 15,000 archivos  
  Documentos legales: ~300GB, 125,000 archivos
  Planos y blueprints: ~150GB, 35,000 archivos
  Contratos y formularios: ~200GB, 85,000 archivos
  
Total Volume: ~2.65TB, 710,000 archivos
```

---

## 📊 Análisis del Estado Actual

### 🏗️ Arquitectura Actual

#### **Storage Infrastructure**
```yaml
Servidor Principal:
  Ubicación: /var/www/inmotech/uploads/
  Filesystem: ext4
  Capacidad: 3TB
  Uso actual: 2.65TB (88% utilización)
  
Estructura de Carpetas:
  /uploads/
    ├── properties/
    │   ├── photos/          # 1.2TB
    │   ├── videos/          # 800GB  
    │   └── documents/       # 300GB
    ├── agents/
    │   ├── profiles/        # 50GB
    │   └── certifications/  # 25GB
    └── contracts/
        ├── templates/       # 15GB
        └── signed/          # 200GB
```

#### **Database References**
```sql
-- Tablas con referencias a archivos
properties_files: 625,000 registros
  - file_path (VARCHAR 500)
  - file_type (ENUM: 'photo', 'video', 'document', 'blueprint')
  - upload_date (TIMESTAMP)
  - file_size (BIGINT bytes)

user_files: 85,000 registros  
  - agent_id (INT)
  - file_path (VARCHAR 500)
  - category (ENUM: 'profile', 'certification', 'contract')

contract_files: 45,000 registros
  - contract_id (INT) 
  - document_path (VARCHAR 500)
  - document_type (VARCHAR 100)
```

---

### 🎯 Arquitectura de Destino

#### **AWS S3 Structure**
```yaml
Buckets Strategy:
  inmotech-prod-media:
    Purpose: Fotos, videos, media content
    Access: Public read via CloudFront
    Storage Class: Standard
    
  inmotech-prod-documents:
    Purpose: Documentos legales, contratos
    Access: Authenticated only
    Storage Class: Standard-IA
    Encryption: AES-256
    
  inmotech-prod-backups:
    Purpose: Backups y archival
    Storage Class: Glacier
    Lifecycle: Auto-transition after 90 days
```

#### **CDN Configuration**
```yaml
CloudFront Distribution:
  Origin: inmotech-prod-media bucket
  Caching: 
    Images: 7 days TTL
    Videos: 30 days TTL
    Documents: 1 day TTL
  Compression: Enabled for all file types
  Edge Locations: Global distribution
```

---

## 🚀 Estrategia de Migración

### 📋 Metodología: "Big Bang Migration"

**Justificación:** 
- Volumen manejable (2.65TB)
- Ventana de mantenimiento disponible
- Reducir complejidad de sincronización
- Minimizar riesgo de inconsistencias

### ⏱️ Cronograma de Ejecución

#### **Fase 1: Preparación (17-18 Feb)**
```yaml
17 Feb - Setup Infraestructura:
  09:00-12:00: Creación de S3 buckets
  12:00-15:00: Configuración de IAM roles y políticas  
  15:00-18:00: Setup de CloudFront distribution
  
18 Feb - Testing y Validación:
  09:00-11:00: Scripts de migración testing
  11:00-13:00: Performance benchmarks
  13:00-15:00: Rollback procedures testing
  15:00-17:00: Team training y final review
```

#### **Fase 2: Migración (19-21 Feb)**
```yaml
19 Feb - Migración de Media (Día 1):
  20:00-08:00: Fotos de propiedades (1.2TB) 
  Target: 150GB/hora → 8 horas estimado
  
20 Feb - Migración de Videos y Documentos (Día 2):  
  20:00-03:00: Videos tours virtuales (800GB)
  03:00-08:00: Documentos legales (300GB + 200GB)
  
21 Feb - Finalización y Validación (Día 3):
  20:00-22:00: Archivos restantes (150GB)
  22:00-24:00: Validación de integridad
  00:00-02:00: Update de database references  
  02:00-06:00: Testing completo del sistema
```

#### **Fase 3: Go-Live (22-24 Feb)**
```yaml
22 Feb - Soft Launch:
  08:00: Activación del nuevo sistema
  08:00-18:00: Monitoring intensivo
  
23 Feb - Pruebas de Usuario:
  09:00-17:00: UAT con grupo piloto de agentes
  
24 Feb - Full Launch:
  08:00: Apertura a todos los usuarios
```

---

## 🛠️ Herramientas y Scripts de Migración

### 📂 AWS CLI Migration Script

#### **Script Principal: `migrate_files.sh`**
```bash
#!/bin/bash

# Configuración
SOURCE_DIR="/var/www/inmotech/uploads"
S3_MEDIA_BUCKET="s3://inmotech-prod-media"
S3_DOCS_BUCKET="s3://inmotech-prod-documents"
LOG_FILE="/var/log/migration_$(date +%Y%m%d_%H%M%S).log"

# Función de logging
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a $LOG_FILE
}

# Migración de fotos de propiedades
migrate_photos() {
    log "Iniciando migración de fotos de propiedades..."
    aws s3 sync $SOURCE_DIR/properties/photos/ $S3_MEDIA_BUCKET/properties/photos/ \
        --storage-class STANDARD \
        --metadata-directive REPLACE \
        --metadata "migrated-date=$(date -u +%Y-%m-%dT%H:%M:%SZ)" \
        --progress
    
    PHOTOS_EXIT_CODE=$?
    if [ $PHOTOS_EXIT_CODE -eq 0 ]; then
        log "✅ Migración de fotos completada exitosamente"
    else
        log "❌ Error en migración de fotos. Exit code: $PHOTOS_EXIT_CODE"
        return 1
    fi
}

# Migración de videos
migrate_videos() {
    log "Iniciando migración de videos..."
    aws s3 sync $SOURCE_DIR/properties/videos/ $S3_MEDIA_BUCKET/properties/videos/ \
        --storage-class STANDARD \
        --metadata "migrated-date=$(date -u +%Y-%m-%dT%H:%M:%SZ)" \
        --progress
    
    VIDEOS_EXIT_CODE=$?
    if [ $VIDEOS_EXIT_CODE -eq 0 ]; then
        log "✅ Migración de videos completada"
    else
        log "❌ Error en migración de videos. Exit code: $VIDEOS_EXIT_CODE"
        return 1
    fi
}

# Migración de documentos (con encriptación)
migrate_documents() {
    log "Iniciando migración de documentos legales..."
    aws s3 sync $SOURCE_DIR/properties/documents/ $S3_DOCS_BUCKET/properties/documents/ \
        --storage-class STANDARD_IA \
        --sse AES256 \
        --metadata "migrated-date=$(date -u +%Y-%m-%dT%H:%M:%SZ),confidential=true" \
        --progress
        
    aws s3 sync $SOURCE_DIR/contracts/ $S3_DOCS_BUCKET/contracts/ \
        --storage-class STANDARD_IA \
        --sse AES256 \
        --metadata "migrated-date=$(date -u +%Y-%m-%dT%H:%M:%SZ),confidential=true" \
        --progress
    
    DOCS_EXIT_CODE=$?
    if [ $DOCS_EXIT_CODE -eq 0 ]; then
        log "✅ Migración de documentos completada"
    else
        log "❌ Error en migración de documentos. Exit code: $DOCS_EXIT_CODE"
        return 1
    fi
}

# Ejecutar migraciones
main() {
    log "🚀 Iniciando migración completa de archivos"
    
    migrate_photos
    if [ $? -ne 0 ]; then exit 1; fi
    
    migrate_videos  
    if [ $? -ne 0 ]; then exit 1; fi
    
    migrate_documents
    if [ $? -ne 0 ]; then exit 1; fi
    
    log "✅ Migración completa finalizada exitosamente"
}

# Ejecutar script principal
main
```

---

### 🗄️ Database Migration Scripts

#### **Script: `update_file_paths.sql`**
```sql
-- Actualización masiva de rutas en base de datos
-- Se ejecuta DESPUÉS de confirmar migración exitosa

-- 1. Backup de tablas antes de modificar
CREATE TABLE properties_files_backup AS SELECT * FROM properties_files;
CREATE TABLE user_files_backup AS SELECT * FROM user_files;
CREATE TABLE contract_files_backup AS SELECT * FROM contract_files;

-- 2. Update properties_files paths
UPDATE properties_files 
SET file_path = CASE 
    WHEN file_type IN ('photo', 'video') 
    THEN CONCAT('https://cdn.inmotech.com/', SUBSTRING(file_path, 26))
    WHEN file_type IN ('document', 'blueprint')
    THEN CONCAT('https://s3.amazonaws.com/inmotech-prod-documents/', SUBSTRING(file_path, 26))
    ELSE file_path
END,
updated_at = NOW()
WHERE file_path LIKE '/var/www/inmotech/uploads/%';

-- 3. Update user_files paths  
UPDATE user_files
SET file_path = CASE
    WHEN category IN ('profile') 
    THEN CONCAT('https://cdn.inmotech.com/', SUBSTRING(file_path, 26))
    WHEN category IN ('certification', 'contract')
    THEN CONCAT('https://s3.amazonaws.com/inmotech-prod-documents/', SUBSTRING(file_path, 26))
    ELSE file_path  
END,
updated_at = NOW()
WHERE file_path LIKE '/var/www/inmotech/uploads/%';

-- 4. Update contract_files paths
UPDATE contract_files
SET document_path = CONCAT(
    'https://s3.amazonaws.com/inmotech-prod-documents/', 
    SUBSTRING(document_path, 26)
),
updated_at = NOW() 
WHERE document_path LIKE '/var/www/inmotech/uploads/%';

-- 5. Verificación de resultados
SELECT 
    'properties_files' as table_name,
    COUNT(*) as total_files,
    COUNT(CASE WHEN file_path LIKE 'https://%' THEN 1 END) as migrated_files,
    COUNT(CASE WHEN file_path LIKE '/var/www/%' THEN 1 END) as pending_files
FROM properties_files
UNION ALL
SELECT 
    'user_files' as table_name,
    COUNT(*) as total_files, 
    COUNT(CASE WHEN file_path LIKE 'https://%' THEN 1 END) as migrated_files,
    COUNT(CASE WHEN file_path LIKE '/var/www/%' THEN 1 END) as pending_files
FROM user_files
UNION ALL
SELECT 
    'contract_files' as table_name,
    COUNT(*) as total_files,
    COUNT(CASE WHEN document_path LIKE 'https://%' THEN 1 END) as migrated_files,
    COUNT(CASE WHEN document_path LIKE '/var/www/%' THEN 1 END) as pending_files
FROM contract_files;
```

---

#### **Script: `migration_validation.sql`**
```sql
-- Queries de validación post-migración
-- Verificar integridad de datos después de actualizar paths

-- 1. Verificar que todos los archivos tienen nuevas URLs
SELECT 
    table_name,
    old_path_count,
    new_path_count,
    CASE 
        WHEN old_path_count = 0 THEN '✅ Migrado'
        ELSE '❌ Pendiente' 
    END as status
FROM (
    SELECT 
        'properties_files' as table_name,
        COUNT(CASE WHEN file_path LIKE '/var/www/%' THEN 1 END) as old_path_count,
        COUNT(CASE WHEN file_path LIKE 'https://%' THEN 1 END) as new_path_count
    FROM properties_files
    UNION ALL
    SELECT 
        'user_files' as table_name,
        COUNT(CASE WHEN file_path LIKE '/var/www/%' THEN 1 END) as old_path_count,
        COUNT(CASE WHEN file_path LIKE 'https://%' THEN 1 END) as new_path_count
    FROM user_files
    UNION ALL
    SELECT 
        'contract_files' as table_name,
        COUNT(CASE WHEN document_path LIKE '/var/www/%' THEN 1 END) as old_path_count,
        COUNT(CASE WHEN document_path LIKE 'https://%' THEN 1 END) as new_path_count
    FROM contract_files
) as migration_stats;

-- 2. Verificar distribución de archivos por tipo
SELECT 
    file_type,
    COUNT(*) as total_files,
    COUNT(CASE WHEN file_path LIKE 'https://cdn.inmotech.com/%' THEN 1 END) as cdn_files,
    COUNT(CASE WHEN file_path LIKE 'https://s3.amazonaws.com/%' THEN 1 END) as s3_files,
    AVG(file_size) / (1024*1024) as avg_size_mb
FROM properties_files 
GROUP BY file_type;

-- 3. Identificar posibles archivos problemáticos
SELECT 
    file_path,
    file_type,
    file_size,
    upload_date,
    'Path no migrado' as issue
FROM properties_files 
WHERE file_path LIKE '/var/www/%'
UNION ALL
SELECT 
    file_path,
    'user_file' as file_type, 
    0 as file_size,
    created_at as upload_date,
    'User path no migrado' as issue
FROM user_files
WHERE file_path LIKE '/var/www/%'
ORDER BY upload_date DESC;
```

---

## 📊 Validación de Integridad

### 🔍 File Integrity Checks

#### **Script: `validate_migration.py`**
```python
#!/usr/bin/env python3
"""
Script de validación de integridad post-migración
Verifica que todos los archivos se migraron correctamente
"""

import boto3
import hashlib
import os
import mysql.connector
from datetime import datetime
import logging

# Configuración
AWS_REGION = 'us-west-2'
S3_MEDIA_BUCKET = 'inmotech-prod-media'
S3_DOCS_BUCKET = 'inmotech-prod-documents'
SOURCE_DIR = '/var/www/inmotech/uploads'

# Setup logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('migration_validation.log'),
        logging.StreamHandler()
    ]
)

class MigrationValidator:
    def __init__(self):
        self.s3_client = boto3.client('s3', region_name=AWS_REGION)
        self.db_connection = self._get_db_connection()
        
    def _get_db_connection(self):
        """Conexión a base de datos"""
        return mysql.connector.connect(
            host='localhost',
            user='inmotech_user',
            password=os.environ['DB_PASSWORD'],
            database='inmotech'
        )
    
    def calculate_file_hash(self, file_path):
        """Calcula MD5 hash de un archivo"""
        hash_md5 = hashlib.md5()
        try:
            with open(file_path, "rb") as f:
                for chunk in iter(lambda: f.read(4096), b""):
                    hash_md5.update(chunk)
            return hash_md5.hexdigest()
        except FileNotFoundError:
            return None
    
    def get_s3_file_hash(self, bucket, key):
        """Obtiene ETag (MD5) de archivo en S3"""
        try:
            response = self.s3_client.head_object(Bucket=bucket, Key=key)
            etag = response['ETag'].strip('"')
            return etag
        except Exception as e:
            logging.error(f"Error obteniendo hash de S3 {bucket}/{key}: {e}")
            return None
    
    def validate_file_integrity(self):
        """Valida integridad de archivos migrados"""
        logging.info("🔍 Iniciando validación de integridad de archivos...")
        
        validation_results = {
            'total_files': 0,
            'valid_files': 0, 
            'invalid_files': 0,
            'missing_files': 0,
            'errors': []
        }
        
        # Obtener lista de archivos de la base de datos
        cursor = self.db_connection.cursor()
        cursor.execute("""
            SELECT file_path, file_type, file_size 
            FROM properties_files 
            WHERE file_path NOT LIKE '/var/www/%'
            LIMIT 1000  -- Validar muestra representativa
        """)
        
        db_files = cursor.fetchall()
        validation_results['total_files'] = len(db_files)
        
        for db_file_path, file_type, expected_size in db_files:
            try:
                # Determinar bucket y key según el tipo de archivo
                if 'cdn.inmotech.com' in db_file_path:
                    bucket = S3_MEDIA_BUCKET
                    key = db_file_path.replace('https://cdn.inmotech.com/', '')
                elif 's3.amazonaws.com' in db_file_path:
                    bucket = S3_DOCS_BUCKET  
                    key = db_file_path.split('/')[-1]  # Simplified key extraction
                else:
                    validation_results['errors'].append(f"Invalid path format: {db_file_path}")
                    validation_results['invalid_files'] += 1
                    continue
                
                # Verificar existencia y tamaño en S3
                try:
                    s3_response = self.s3_client.head_object(Bucket=bucket, Key=key)
                    s3_size = s3_response['ContentLength']
                    
                    if s3_size == expected_size:
                        validation_results['valid_files'] += 1
                        logging.debug(f"✅ Valid: {key}")
                    else:
                        validation_results['invalid_files'] += 1
                        validation_results['errors'].append(
                            f"Size mismatch {key}: expected {expected_size}, got {s3_size}"
                        )
                        
                except Exception as e:
                    validation_results['missing_files'] += 1
                    validation_results['errors'].append(f"Missing file {key}: {e}")
                    
            except Exception as e:
                validation_results['invalid_files'] += 1
                validation_results['errors'].append(f"Error validating {db_file_path}: {e}")
        
        return validation_results
    
    def generate_validation_report(self, results):
        """Genera reporte de validación"""
        success_rate = (results['valid_files'] / results['total_files']) * 100
        
        report = f"""
📊 MIGRATION VALIDATION REPORT
Generated: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}

📈 SUMMARY:
Total files validated: {results['total_files']:,}
Valid files: {results['valid_files']:,} ({success_rate:.1f}%)
Invalid files: {results['invalid_files']:,}
Missing files: {results['missing_files']:,}

🎯 SUCCESS CRITERIA:
✅ Target: >99.5% success rate
{'✅ PASSED' if success_rate >= 99.5 else '❌ FAILED'}: {success_rate:.2f}%

❌ ERRORS FOUND ({len(results['errors'])}):
"""
        
        for error in results['errors'][:10]:  # Show first 10 errors
            report += f"- {error}\n"
            
        if len(results['errors']) > 10:
            report += f"... and {len(results['errors']) - 10} more errors\n"
        
        return report

if __name__ == "__main__":
    validator = MigrationValidator()
    results = validator.validate_file_integrity()
    report = validator.generate_validation_report(results)
    
    # Save report
    with open('migration_validation_report.txt', 'w') as f:
        f.write(report)
    
    print(report)
    
    # Exit with error if validation failed
    if results['valid_files'] / results['total_files'] < 0.995:
        exit(1)
```

---

## 🚨 Plan de Contingencia y Rollback

### 🔄 Estrategia de Rollback

#### **Trigger Conditions para Rollback:**
```yaml
Automatic Rollback:
  - Success rate < 95% after 4 horas de migración
  - Más de 50 errores críticos detectados
  - Performance degradation > 200% vs baseline
  - Security breach o unauthorized access detected

Manual Rollback Decision:
  - User satisfaction < 6/10 in first 24h
  - Business critical functionality broken
  - Excessive support tickets (>20% increase)
  - Executive decision based on business impact
```

#### **Rollback Procedure:**

**Step 1: Immediate Isolation (15 minutes)**
```bash
# 1. Revert DNS para CDN
aws route53 change-resource-record-sets \
    --hosted-zone-id Z123EXAMPLE \
    --change-batch file://rollback-dns.json

# 2. Restore database paths
mysql -u root -p inmotech < rollback_database_paths.sql

# 3. Reactivate local file serving
sudo systemctl restart apache2
sudo service nginx reload
```

**Step 2: Full System Rollback (30 minutes)**
```bash
# 1. Database restore desde backup
mysql -u root -p inmotech < properties_files_backup.sql

# 2. Application code rollback
git checkout production-stable
sudo service apache2 restart

# 3. Cleanup AWS resources (opcional)
# aws s3 rb s3://inmotech-prod-media --force
# aws s3 rb s3://inmotech-prod-documents --force
```

---

### 💾 Backup Strategy

#### **Pre-Migration Backups**

**1. Database Backup:**
```bash
# Full database dump con timestamps
mysqldump -u root -p inmotech \
    --single-transaction \
    --routines \
    --triggers \
    > inmotech_backup_pre_migration_$(date +%Y%m%d_%H%M%S).sql

# Backup específico de tablas con file paths
mysqldump -u root -p inmotech \
    properties_files user_files contract_files \
    > file_tables_backup_$(date +%Y%m%d_%H%M%S).sql
```

**2. File System Backup:**
```bash
# Crear snapshot del directorio de uploads
tar -czf uploads_backup_$(date +%Y%m%d).tar.gz \
    -C /var/www/inmotech/ uploads/

# Verificar integridad del backup
tar -tzf uploads_backup_$(date +%Y%m%d).tar.gz | wc -l
```

**3. Configuration Backup:**
```bash
# Backup de configuraciones críticas
cp -r /etc/apache2/ /backup/apache2_$(date +%Y%m%d)/
cp -r /etc/nginx/ /backup/nginx_$(date +%Y%m%d)/
cp /var/www/inmotech/.env /backup/.env_$(date +%Y%m%d)
```

---

## 📈 Monitoring y Métricas

### 🔍 Real-time Monitoring durante Migración

#### **Key Metrics to Track:**
```yaml
Migration Progress:
  - Files transferred per hour
  - Data volume transferred (GB/hour) 
  - Error rate percentage
  - Network bandwidth utilization
  - S3 PUT success rate

System Performance:
  - Database query response time
  - Application server CPU/memory
  - Network latency to AWS
  - Local disk I/O utilization

Business Metrics:
  - User complaints/support tickets
  - Feature availability uptime
  - File access success rate
  - Page load times (file-heavy pages)
```

#### **Monitoring Dashboard Setup:**
```yaml
CloudWatch Dashboard:
  Widgets:
    - S3 PUT/GET request metrics
    - CloudFront cache hit ratio
    - Error rate trends
    - Data transfer costs

Application Monitoring:
  - Custom metrics para file access
  - Database query performance  
  - User session success rates
  - API response times

Alerting Thresholds:
  Critical: Error rate > 5%
  Warning: Transfer speed < 100GB/hour
  Info: Unusual traffic patterns detected
```

---

### 📊 Criterios de Éxito y KPIs

#### **Criterios de Éxito de Migración:**
```yaml
Technical KPIs:
  File Integrity: >99.9% archivos sin corrupción
  Transfer Success: >99.5% archivos migrados exitosamente
  Performance: <100ms additional latency for file access
  Availability: >99.9% uptime durante migración

Business KPIs:
  User Experience: <5% increase en support tickets
  Feature Adoption: >80% users accessing files day 1
  Performance Perception: <10% users reportan slowness
  Training Effectiveness: <2% users require additional help
```

#### **Post-Migration Validation:**
```yaml
Week 1 Metrics:
  - File access success rate: Target >99.5%
  - Average file load time: Target <2 seconds
  - CDN cache hit ratio: Target >85%
  - Storage cost optimization: Target 30% reduction vs local
  
Month 1 Metrics:
  - User satisfaction score: Target >8/10
  - System performance vs baseline: Target <110%
  - Support ticket resolution: Target <4 hours avg
  - Business continuity: Target 100% critical features operational
```

---

## 📝 Cronograma de Ejecución Detallado

### 🗓️ Cronograma Hora por Hora

#### **Viernes 17 Feb 2026 - Día 1: Setup**
```
08:00-09:00 ☕ Team kickoff meeting
            - Review final migration plan  
            - Confirm roles and responsibilities
            - Verify all tools and access ready

09:00-12:00 🏗️ AWS Infrastructure Setup
            - Create S3 buckets con security policies
            - Configure IAM roles y cross-account access
            - Setup CloudFront distribution
            - Configure Route53 DNS (staging)

12:00-13:00 🍽️ Lunch break

13:00-15:00 📊 Monitoring Setup  
            - CloudWatch dashboards configuration
            - Custom metrics y alarms setup
            - Slack/email notification channels
            - Backup verification scripts

15:00-17:00 🧪 Testing y Validation
            - Migration scripts testing con subset de datos
            - Rollback procedures testing
            - Network bandwidth testing
            - Database backup testing

17:00-18:00 📋 Final Prep
            - Team final review y Q&A
            - Migration checklist verification  
            - On-call schedule confirmation
            - Stakeholder communication (go/no-go)
```

---

#### **Sábado 18 Feb 2026 - Día 2: Final Testing**
```
09:00-11:00 🔬 End-to-End Testing
            - Full migration workflow test con 1GB sample
            - Database path updates testing
            - CDN caching behavior verification
            - Performance benchmarks establishment

11:00-13:00 📚 Team Training
            - Migration scripts walkthrough
            - Troubleshooting scenarios practice
            - Monitoring tools training
            - Emergency procedures review

13:00-14:00 🍽️ Lunch break

14:00-16:00 ✅ Final Validation
            - All systems green check
            - Backup integrity verification  
            - Network connectivity to AWS confirmed
            - Database performance baseline captured

16:00-17:00 📞 Stakeholder Communication
            - Final go/no-go decision meeting
            - User communication preparation
            - Support team briefing
            - Executive summary delivery
```

---

#### **Domingo 19 Feb 2026 - Día 3: MIGRATION START**
```
19:00-19:30 🚀 Pre-Migration Final Check
            ✅ All systems operational
            ✅ Team on standby  
            ✅ Monitoring active
            ✅ Backups verified

19:30-20:00 🛑 Maintenance Mode
            - Enable maintenance page
            - Stop file uploads temporarily
            - Database final backup
            - Announce migration start

20:00-04:00 📷 PHOTOS MIGRATION (8 horas)
            Target: 1.2TB, 450,000 archivos
            Expected Rate: 150GB/hora
            
            Progress Checkpoints:
            22:00: 25% complete checkpoint
            00:00: 50% complete checkpoint  
            02:00: 75% complete checkpoint
            04:00: 100% complete verification

04:00-06:00 ✅ Photos Validation  
            - File integrity checks
            - Database path updates for photos
            - CDN cache warming
            - Performance testing
```

---

#### **Lunes 20 Feb 2026 - Día 4: VIDEOS & DOCS**
```
20:00-03:00 🎥 VIDEOS MIGRATION (7 horas)
            Target: 800GB, 15,000 archivos
            Expected Rate: 114GB/hora
            
            Progress Checkpoints:
            22:00: 25% complete (200GB)
            00:00: 50% complete (400GB)
            01:30: 75% complete (600GB)  
            03:00: 100% complete (800GB)

03:00-08:00 📄 DOCUMENTS MIGRATION (5 horas)
            Target: 500GB (300GB + 200GB), 210,000 archivos
            Expected Rate: 100GB/hora
            
            Progress Checkpoints:
            04:30: 30% complete (150GB)
            06:00: 60% complete (300GB)
            07:30: 90% complete (450GB)
            08:00: 100% complete (500GB)
```

---

#### **Martes 21 Feb 2026 - Día 5: FINALIZATION**
```
20:00-22:00 🏁 FINAL FILES MIGRATION
            Target: 150GB restantes (blueprints, misc)
            Expected: 2 horas máximo
            
22:00-00:00 🔍 COMPREHENSIVE VALIDATION
            - Execute validation scripts
            - File integrity checks
            - Database consistency verification
            - Performance baseline testing

00:00-02:00 💾 DATABASE UPDATES
            - Update all file_path references
            - Migrate user_files paths
            - Update contract_files paths  
            - Verify foreign key constraints

02:00-06:00 🧪 FULL SYSTEM TESTING
            - End-to-end user workflows
            - File upload/download testing
            - Mobile app testing
            - API integration testing
            - Performance metrics capture

06:00-08:00 📊 FINAL VALIDATION REPORT
            - Generate migration success report
            - Performance comparison analysis
            - User communication preparation
            - Go-live decision preparation
```

---

## 🎯 Checklist de Validación Final

### ✅ Pre-Go-Live Checklist

#### **🔧 Technical Validation**
```yaml
Infrastructure:
□ S3 buckets operational y accesible
□ CloudFront distribution serving content correctly  
□ IAM permissions working para todas las operaciones
□ SSL certificates válidos y funcionando
□ DNS routing a nuevas URLs funcionando

Data Integrity:
□ >99.9% archivos migrados exitosamente
□ File integrity validation passed
□ Database paths actualizados correctamente
□ No archivos duplicados o corruptos
□ Backup procedures verified y disponibles

Performance:
□ File access latency <2 segundos promedio  
□ CDN cache hit ratio >80%
□ Database query performance dentro de baseline
□ Mobile app performance aceptable
□ No memory leaks o resource issues detectados
```

#### **👥 User Experience Validation**
```yaml
Core Functionality:
□ File upload funcionando desde todos los devices
□ File download funcionando correctamente  
□ File sharing con clientes operacional
□ Search y filtering de archivos working
□ Bulk operations (delete, move) funcionando

Mobile Experience:  
□ iOS app file operations working
□ Android app file operations working
□ Mobile web interface responsive
□ Offline functionality working
□ Push notifications para file updates working

Agent Workflows:
□ Property listing creation con files working
□ Client file sharing operacional
□ Bulk photo upload para properties working  
□ Contract document management working
□ Report generation con embedded files working
```

#### **🛟 Support Readiness**
```yaml
Documentation:
□ User migration guide publicado
□ FAQ actualizado con common issues
□ Troubleshooting guide para support team  
□ API documentation actualizada
□ Mobile app release notes publicadas

Team Preparation:
□ Support team trained en nuevas funcionalidades
□ Escalation procedures establecidos
□ On-call engineer schedule confirmed
□ Rollback procedures documentadas y tested
□ Communication plan activated
```

---

## 📞 Contactos y Responsabilidades

### 👨‍💼 Equipo de Migración

#### **🎯 Migration Lead: Ricardo Fernández**
- **Responsabilidades:** Coordinación general, AWS setup, scripts de migración
- **Disponibilidad:** 24/7 durante migración
- **Contacto:** 
  - 📱 +34 666 789 012
  - 📧 ricardo.fernandez@inmotech.com
  - 💬 Slack: @ricardo.devops

#### **🗄️ Data Architect: Carmen López**
- **Responsabilidades:** Database migrations, data integrity, validation
- **Disponibilidad:** 20:00-08:00 durante migración nocturna
- **Contacto:**
  - 📱 +34 666 123 456  
  - 📧 carmen.lopez@inmotech.com
  - 💬 Slack: @carmen.backend

#### **⚙️ Infrastructure: David Chen**  
- **Responsabilidades:** CDN setup, performance monitoring, troubleshooting
- **Disponibilidad:** 18:00-06:00 durante migración
- **Contacto:**
  - 📱 +34 666 345 678
  - 📧 david.chen@inmotech.com
  - 💬 Slack: @david.frontend

#### **📊 QA Lead: Ana Martín**
- **Responsabilidades:** Testing, validation, user acceptance
- **Disponibilidad:** On-call para validation crítica
- **Contacto:**
  - 📱 +34 666 456 789
  - 📧 ana.martin@inmotech.com
  - 💬 Slack: @ana.qa

---

### 🚨 Escalation Matrix

#### **Level 1 - Technical Issues**
```yaml
Trigger: Script errors, performance issues, minor data inconsistencies
Response Time: <30 minutes  
Primary: Migration team (Ricardo, Carmen, David)
Actions: 
  - Investigate y fix technical issues
  - Adjust migration parameters
  - Continue with modified timeline si es necesario
```

#### **Level 2 - Major Issues**  
```yaml
Trigger: >5% error rate, significant performance degradation, security concerns
Response Time: <15 minutes
Primary: Migration Lead + CTO
Actions:
  - Stop migration si es necesario
  - Assess rollback necessity
  - Communicate status to stakeholders
```

#### **Level 3 - Critical Issues**
```yaml
Trigger: Data corruption, security breach, system unavailability
Response Time: <5 minutes
Primary: CTO + Executive team
Actions:
  - Immediate rollback execution
  - Emergency communication to all stakeholders
  - Post-incident analysis y recovery planning
```

---

**Fecha de Creación:** 21/11/2025
**Última Actualización:** 21/11/2025
**Versión:** 1.0